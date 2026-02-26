---
id: m-9bab
status: open
deps: [m-5870, m-3f0b, m-fd36, m-a3fe]
created: 2026-02-20T01:13:10Z
type: task
priority: 2
assignee: Adam Avenir
updated: 2026-02-20T01:13:25Z
---
# Migrate orchestrator output to llm/output/

## Problem

Each orchestrator writes output to a different location with different naming conventions:
- QA: `@base/qa/runs/YYYY-MM-DD-N/` (via custom `@resolveRunDir`)
- Polish: `@base/pipeline/current-run/` (fixed path)
- Review: `@base/runs/review/` (fixed path)
- Review-Docs: `@base/runs/review-docs/` (fixed path)
- PR-Review: `@outBase/YYYY-MM-DD/` (dated, single per day)
- Doc-Audit: inline `@base/runs/doc-audit-YYYY-MM-DD/`
- Doc-Writer: inline output paths
- J2BD: `@base/j2bd/<topic>/runs/YYYY-MM-DD-N/`

Convention (per `llm/CONVENTIONS.md`) is to standardize on `llm/output/<name>-YYYY-MM-DD-N/`.

## Required changes

For each orchestrator, replace custom run directory logic with:

```mlld
import { @resolveOutputDir } from @lib/runs
var @runDir = @resolveOutputDir("<name>", @p.run)
```

### Per-orchestrator changes

| Orchestrator | Current output path | New name arg | Notes |
|---|---|---|---|
| QA | `@base/qa/runs/YYYY-MM-DD-N/` | `"qa"` | Replace `@resolveRunDir`/`@nextIteration`/`@findLastIncomplete` with `@resolveOutputDir`. QA's completion-marker logic for auto-increment is QA-specific — keep it but adapt to use the new output root. |
| Polish | `@base/pipeline/current-run/` | `"polish"` | Fixed path becomes dated. Checkpoint handles resumption, not the path. |
| Review | `@base/runs/review/` | `"review"` | Fixed path becomes dated. |
| Review-Docs | `@base/runs/review-docs/` | `"review-docs"` | Fixed path becomes dated. |
| PR-Review | `@outBase/YYYY-MM-DD/` | `"pr-review"` | Already dated, just move under `llm/output/`. |
| Doc-Audit | `@base/runs/doc-audit-YYYY-MM-DD/` | `"doc-audit"` | Move under `llm/output/`. |
| Doc-Writer | inline | `"doc-writer"` | Add run dir if it doesn't have one. |
| J2BD | `@base/j2bd/<topic>/runs/YYYY-MM-DD-N/` | `"j2bd-<topic>"` | Dynamic name from config topic. |

### Also update

- **`.gitignore`**: Verify `llm/output/` is gitignored. Remove old output path entries if they exist (e.g., `qa/runs/`, `pipeline/`, `runs/`).
- **Do NOT move old run data** — leave existing output in old locations.

### Also import `@lib/common` and `@lib/events` where applicable

Since this ticket touches every orchestrator, also add `import { @mkdirp } from @lib/common` (and other common imports) where the local definitions were replaced by m-5870.

### Also add `bail` to all `exe llm` wrappers

Every orchestrator except QA has bare pass-through `exe llm` wrappers that don't check for failure. If an LLM call fails (agent crashes, API timeout, marker file not written), the empty result gets **cached by checkpoint** and the pipeline continues with garbage data. On re-run, the bad cached result is served — the call never retries.

QA's `llm/run/qa/lib/claude.mld:63-69` has the correct pattern:

```mlld
>> GOOD: bail on failure prevents caching empty results
exe llm @claudeSession(prompt, model, tools, markerFile, sessionId, timeout) = [
  let @result = @runSession(@prompt, @model, @tools, @markerFile, @sessionId, @t)
  when !@result => bail `Agent exited without completing (marker file not written).`
  => @result
]
```

Apply this pattern to all other orchestrators. The bare pass-through form must become a block with a bail guard:

```mlld
>> BEFORE (bad — caches empty results on failure)
exe llm @llmCall(prompt, model, dir, tools, outPath) = @claudePoll(@prompt, @model, @dir, @tools, @outPath)

>> AFTER (good — bail prevents caching, re-run retries the failed call)
exe llm @llmCall(prompt, model, dir, tools, outPath) = [
  let @result = @claudePoll(@prompt, @model, @dir, @tools, @outPath)
  when !@result => bail `LLM call failed (no output written to @outPath)`
  => @result
]
```

**Wrappers that need this fix:**

| Orchestrator | Function(s) | File:Line |
|---|---|---|
| Review | `@llmCall` | `review/index.mld:51` |
| Review-Docs | `@callReview`, `@callVerify`, `@callRevise` | `review-docs/index.mld:50-52` |
| Doc-Audit | `@callReview` | `doc-audit/index.mld:24` |
| Doc-Writer | `@callWriter` | `doc-writer/index.mld:46` |
| J2BD | `@callDecision`, `@callWorker` | `j2bd/index.mld:51-52` |
| PR-Review | `@codexReview` | `pr-review/index.mld:125` (sh block — check exit code or output) |

## Acceptance criteria

- Every orchestrator uses `@resolveOutputDir` from `@lib/runs` (or a thin wrapper around it).
- All new run output lands in `llm/output/<name>-YYYY-MM-DD-N/`.
- `--run latest` resumes the most recent run for each orchestrator.
- Every `exe llm` wrapper checks for empty/falsy results and calls `bail` on failure.
- Existing tests and checkpoint caches are not broken (checkpoint paths are separate from output paths).

