---
id: m-1c1f
status: closed
deps: []
created: 2026-02-19T20:53:37Z
type: feature
priority: 0
assignee: Adam Avenir
updated: 2026-02-19T21:56:38Z
---
# Named checkpoint directive

Add a `checkpoint "name"` directive that registers a named resume point in script execution. Enables `--resume "name"` to re-run from top with cache invalidation of LLM calls produced after the checkpoint.

## Design

- New directive: `checkpoint "name"` — no-op at normal runtime, registers a named position in execution order
- Top-level only — not valid inside function bodies
- Names must be unique within a script, enforced by `mlld validate` and at registration time
- `--resume "name"` re-runs script from top, all LLM calls before the checkpoint hit cache, calls after the checkpoint are invalidated and re-execute
- Checkpoint tracks execution order at runtime (not source position), so it correctly handles conditional branches
- Grammar: simple — `checkpoint` keyword followed by a quoted string
- AST node: `CheckpointDirectiveNode` with `name: string`
- Registry: stored on Environment, records checkpoint names and their execution-order position
- Integrates with existing CheckpointManager for cache invalidation

## Acceptance Criteria

- `checkpoint "name"` parses and validates
- Duplicate checkpoint names produce a validation error
- Checkpoint inside a function body produces a validation error
- `--resume "checkpoint-name"` invalidates cached LLM calls after the named checkpoint
- Checkpoint is a no-op during normal (non-resume) execution
- `mlld validate` checks uniqueness of checkpoint names


**2026-02-19 21:06 UTC:** ## Design clarification: checkpoints are cache cursors, not a separate mechanism

Named checkpoints are a targeting strategy for the single LLM cache, not a separate storage system. One cache, three invalidation strategies:

1. **Automatic** — LLM cache by argument hash (crash recovery)
2. **`--resume @fn`** — invalidate by function name (prompt iteration)  
3. **`--resume "name"`** — invalidate by execution-order position (phase re-entry)

`checkpoint "x"` registers a position in the execution timeline. `--resume "x"` invalidates cached LLM calls that were produced after that position was reached. No state snapshots, no separate cache files.

## Top-level only rationale

Checkpoints inside loops (including `loop()` convergence loops) are not supported. The loop cases are covered by the other two strategies:
- **Crash recovery** in loops: LLM cache handles it — each call has a unique hash based on arguments, so completed iterations hit cache on re-run
- **Prompt changes** in loops: `--resume @fn` invalidates the target function's cache entries across iterations
- **Phase restructuring**: for workflows with distinct phases, pull phases into separate top-level blocks with checkpoints between them

This should be explicit in docs so users understand the three tools and when to reach for each.

**2026-02-19 21:20 UTC:** ## Conditional checkpoints

Checkpoints are valid in two positions:
1. Top-level directive
2. Direct `=>` result inside a **top-level** `when` block

```mlld
checkpoint "always-reached"

when @triage.complexity [
  "high" => checkpoint "deep-path"
  * => checkpoint "quick-path"
]
```

The `when` must itself be top-level — a `when` inside a function body or loop does NOT qualify. `--resume "name"` errors if the checkpoint wasn't reached in the prior run's cache.

Note: the base hooks/checkpoints/resume spec is already implemented. This named checkpoint directive is a new addition to that feature.

**2026-02-19 21:24 UTC:** ## Refined: valid checkpoint positions in `when` blocks

Three forms of top-level `when` support checkpoints as branch results:

### 1. Block conditional (boolean conditions)
```mlld
when [
  @complexity == "high" => checkpoint "deep-path"
  @complexity == "low" => checkpoint "quick-path"
  * => checkpoint "default-path"
]
```

### 2. Value-matching (pattern match on expression)
```mlld
when @triage.complexity [
  "high" => checkpoint "deep-path"
  "medium" => checkpoint "standard-path"
  * => checkpoint "quick-path"
]
```

### 3. Inline conditional (single condition)
```mlld
when @config.expensive => checkpoint "expensive-enabled"
```

### NOT valid:
- Inside `when` blocks that are themselves inside function bodies, loops, or `for` blocks
- Inside block actions (`=> [ ... checkpoint ... ]`) — the checkpoint must be the direct `=>` result, not nested inside a block
- Inside `exe` value-returning when expressions

### Validation rule:
A `checkpoint` is valid if its nearest enclosing scope is either (a) the top-level script body, or (b) a branch result of a `when` whose nearest enclosing scope is the top-level script body. One level of `when` nesting only.

**2026-02-19 21:30 UTC:** ## Checkpoint name values and matching

### String types
Checkpoint names follow mlld's standard string value rules:
- Single quotes: literal (`checkpoint 'phase-one'`)
- Double quotes: interpolate variables (`checkpoint "@pipeline-complete"`)
- Backticks: interpolate variables (`checkpoint `@pipeline-complete``)

Dynamic names (via interpolation) mean static uniqueness validation in `mlld validate` can only check literal strings. Runtime uniqueness is enforced at registration — error on second registration of the same resolved name.

### Prefix matching in --resume
`--resume` supports prefix matching on checkpoint names, consistent with the existing `--resume @fn("prefix...")` pattern:

```bash
# Exact match
mlld run pipeline --resume "data-collection-complete"

# Prefix match
mlld run pipeline --resume "data-col"

# Ambiguous prefix -> error with candidates listed
mlld run pipeline --resume "data"
# Error: ambiguous checkpoint match "data" — matches:
#   "data-collection-complete"
#   "data-processing-complete"
```

No kebab-case enforcement — checkpoint names are strings, consistent with all other mlld string values. Shell quoting handles spaces naturally (`--resume "name of checkpoint"`).
