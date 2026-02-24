---
id: m-b6a9
status: open
deps: [m-1c1f, m-30e4, m-ab2c]
created: 2026-02-19T21:10:04Z
type: task
priority: 0
assignee: Adam Avenir
updated: 2026-02-23T23:37:50Z
links: 
---
# Update orchestrator skill docs to teach checkpoint/resume as primary resilience pattern

## Summary

The orchestrator skill docs (plugins/mlld/skills/orchestrator/) currently teach manual resumption as the primary pattern: events.jsonl, run.json state files, file-existence idempotency checks, --run/--new flags, and run directory resolution. With the checkpoint/resume system (llm labels, named checkpoints, auto-checkpoint), these manual patterns are superseded. The skill docs need to be rewritten to teach the new approach as primary, with manual state management reserved for the cases that genuinely need it.

## The conceptual framework to teach

One cache, three targeting strategies:

1. **Automatic (LLM cache by argument hash)** — crash recovery. Re-run the script, completed llm-labeled calls hit cache. No code changes needed.
2. **--resume @fn** — prompt iteration. "I changed the @judge prompt, redo all its calls." Invalidates by function name.
3. **--resume "checkpoint-name"** — phase re-entry. Named checkpoint directives mark phase boundaries. "Skip to phase 3." Invalidates all LLM calls after the named position.

These are not three separate systems — they're three ways to target invalidation in one cache. A checkpoint directive is a named cursor into the invalidation timeline, not a state snapshot.

### When manual state management is still needed

The checkpoint system owns "don't redo expensive work." But some orchestrators need event logs as DATA inputs:
- **Decision loop orchestrators** (j2bd archetype): the decision agent reads recent events to understand what's been tried, what failed, and what workers reported. This is program logic, not resumption.
- **Cross-phase data pipelines** (polish merge phase): one phase produces metadata (branch names, commit SHAs) that a later phase consumes via the event log.

For these cases, event logging stays — but it should be taught as "decision context" not "resumption infrastructure."

## Files to update

### 1. SKILL.md (main orchestrator guide)
File: plugins/mlld/skills/orchestrator/SKILL.md

**Section: "Best Practices" (lines 30-37)**
Currently says: "Resumable — Append progress to events.jsonl. Idempotency checks (<@outPath>?) skip completed work. Require --new to start a fresh run; default to resuming the latest."

Replace with: "Resilient — Label expensive calls with llm. Crash recovery is automatic. Use checkpoint directives between phases for --resume targeting. Use --new for a fresh run."

**Section: "State Management" (lines 282-315)**
Currently teaches run.json + events.jsonl as the primary state pattern with code examples for logEvent, loadRecentEvents.

Restructure to:
1. Primary: checkpoint system (llm labels + named checkpoints)
2. When you also need event logs: decision context pattern (j2bd-style, events as LLM input)
3. Deprecate: events.jsonl for resumption (replaced by checkpoints)

**Section: "Idempotency" (lines 430-440)**
Currently teaches manual file-existence checks.

Replace with: "LLM cache handles idempotency automatically for llm-labeled calls. Manual checks are only needed for non-LLM side effects (file writes, API calls)."

**Section: "Parallel Processing" (lines 410-428)**
Currently shows manual idempotency check inside for parallel.

Simplify to: plain for parallel with llm-labeled calls. Cache handles skip-if-done.

**Section: "Phase Inference" (lines 478-490)**
Currently teaches filesystem-based phase inference via decision prompt.

Add: named checkpoint directives as the complementary approach for linear multi-phase orchestrators. Phase inference stays valid for decision-loop orchestrators where phases aren't linear.

**Section: "Human Handoff" (lines 442-455)**
Currently shows `mlld run myorch --run @runId` for resume.

Update to: `mlld run myorch --resume "phase-name"` or just `mlld run myorch` (auto-resume via cache).

**New section: "Resilience Model"**
Add after Best Practices. Explain the three-tool model:
- LLM cache (per-call crash recovery)
- --resume @fn (per-function prompt iteration)
- checkpoint "name" (per-phase workflow navigation)

Include the rationale for top-level-only checkpoints and why loops don't need them (LLM cache handles per-iteration resilience, --resume @fn handles prompt changes).

### 2. syntax-reference.md
File: plugins/mlld/skills/orchestrator/syntax-reference.md

**Section: "State persistence" (lines 33-49)**
Keep but reframe as "for decision context only, not for resumption."

**Section: "Idempotency" (lines 51-68)**
Replace with checkpoint-based pattern.

**Add new section: "Checkpoint and Resume"**
```mlld
>> Label expensive calls for automatic caching
exe llm @review(prompt) = @claudePoll(@prompt, "sonnet", "@root", @tools, @outPath)

>> Named checkpoints between phases
checkpoint "collection"
var @data = for parallel(20) @item in @items => llm @collect(@item)

checkpoint "analysis"
var @results = for parallel(20) @item in @items => llm @analyze(@item, @data)

>> Resume from a phase: mlld run pipeline --resume "analysis"
>> Resume a function: mlld run pipeline --resume @analyze
>> Fuzzy resume: mlld run pipeline --resume @analyze("item-50")
>> Fresh run: mlld run pipeline --new
```

### 3. anti-patterns.md
File: plugins/mlld/skills/orchestrator/anti-patterns.md

**Add new anti-pattern: "The Resumption Infrastructure Trap"**

Symptom: Building run directories, event logging, file-existence checks, and retry loops to handle crashes.

```mlld
>> BAD: manual resumption
var @runDir = ...
loop(@maxAttempts) [
  for parallel(N) @item in @items [
    if @exists(@outPath) [ => null ]
    @claudePoll(...)
  ]
  >> check for missing items
  >> retry missing items
]
```

Fix: Use llm labels. Checkpoint handles all of this.

```mlld
>> GOOD: llm label + checkpoint
exe llm @review(item) = @claudePoll(...)
var @results = for parallel(N) @item in @items => @review(@item)
```

### 4. Plugin examples
Files: plugins/mlld/examples/{audit,research,development}/index.mld

Update examples to use:
- llm labels on LLM call wrappers
- checkpoint directives between phases (where applicable)
- Remove manual idempotency checks, retry loops, run directory resolution
- Keep event logging only in development/ (decision context)

### 5. Archetype descriptions in SKILL.md (lines 169-237)
Update the three archetype code examples to show the checkpoint pattern instead of manual state.

## Getting up to speed

- Checkpoint spec: spec-hooks-checkpoints-resume.md (root, 949 lines) — User Guide section (lines ~31-298)
- Named checkpoint ticket: tk show m-1c1f
- Auto-checkpoint ticket: tk show m-30e4
- Orchestrator migration ticket: tk show m-ab2c (includes per-orchestrator event log analysis)
- Current skill docs: plugins/mlld/skills/orchestrator/
- Current examples: plugins/mlld/examples/
- Retry pattern (already added to docs): See "Quality Gates and Retry" in SKILL.md and anti-patterns.md #8
- Pipeline retry docs: docs/src/atoms/syntax/pipelines-retry.md

