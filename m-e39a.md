---
id: m-e39a
status: open
deps: [m-b24a]
created: 2026-02-19T18:39:34Z
type: feature
priority: 0
assignee: Adam Avenir
updated: 2026-02-19T18:39:37Z
---
# Pre-flight deep validation: walk full script tree before execution

## Problem

`mlld run` currently begins execution immediately with no pre-flight validation. If a template (.att) contains an undefined `@var` reference, it only fails at runtime — potentially 20+ minutes into a long-running orchestrator. `mlld validate` only checks a single file statically; it doesn't follow imports or parse templates.

### Real incident

`llm/run/review/prompts/workers/live-test.att` contained `@fn("prefix")` as example text. The template parameters were `(scenarioName, scenarioDesc, specPath, evidenceRules)` — `@fn` was not among them. This only failed 20 minutes into execution when Phase 4 first rendered the template.

## Proposed Solution

### 1. Deep pre-flight validation on `mlld run`

Before any execution begins, walk the full script tree:

1. Parse the entry point (.mld)
2. Follow all `import` statements recursively
3. For each `exe @fn(...) = template "path.att"`, parse the template
4. For each template, check that every `@var` reference (not escaped with `@@`/`\@`) is either:
   - A declared parameter of the enclosing `exe` definition
   - A known built-in (`@now`, `@root`, `@mx.*`, etc.)
5. If any undefined `@var` references are found, **error immediately** with clear diagnostics:
   ```
   error: prompts/workers/live-test.att:15 — undefined variable @fn in template
     defined parameters: scenarioName, scenarioDesc, specPath, evidenceRules
     hint: use @@fn or \@fn for literal @ text
   ```
6. Also surface warnings (anti-patterns, shadowing, etc.) at the top of output

### 2. Warning display behavior

- Show warnings at the top of `mlld run` output before execution begins
- Add `--no-warn` flag to suppress warning output
- Errors always block execution (no flag to override)

### 3. Update `mlld validate` to support deep mode

- `mlld validate <script>` — current single-file behavior (default)
- `mlld validate --deep <script>` — follows imports and templates, same checks as pre-flight
- Or: make deep the default for .mld entry points that have imports

## Implementation Notes

- Entry point: `RunCommand.run()` in `cli/commands/run.ts`, before calling `execute()`
- Template parsing already exists in `interpreter/eval/exe/definition-helpers.ts:133` (`parseTemplateFileNodes`)
- Single-file analysis exists in `cli/commands/analyze.ts` (`analyze()` function)
- The `maskPlainMlldTemplateFences` function masks ````mlld` fenced blocks — plain ``` blocks are NOT masked
- Escape mechanisms `@@var` and `\@var` already work in all contexts (tested in `tests/integration/escaping-at-contexts.test.ts`)

## Scope

- Pre-flight validation runs on every `mlld run` invocation
- Must be fast (parse + static check only, no evaluation)
- Should not add noticeable latency for small scripts
- For large script trees, should still be << 1 second (it's just parsing)

## Acceptance Criteria

- [ ] `mlld run` performs deep tree walk before execution starts
- [ ] Undefined @var in templates produces immediate error with file:line, parameter list, and escape hint
- [ ] Warnings shown at top of output before execution
- [ ] `--no-warn` flag suppresses warning output
- [ ] `mlld validate --deep` follows imports and templates
- [ ] Pre-flight adds negligible latency (< 200ms for typical scripts)
- [ ] Error message includes the escape hint: "use @@var or \@var for literal @ text"

