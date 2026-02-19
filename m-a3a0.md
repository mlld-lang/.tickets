---
id: m-a3a0
status: closed
deps: []
created: 2026-02-18T16:03:02Z
type: task
priority: 0
assignee: Adam Avenir
updated: 2026-02-19T03:14:07Z
---
# Rearchitect review-docs to use check-and-repair decision loop


**2026-02-18 16:03 UTC:** ## Problem

review-docs runs a 3-phase linear pipeline (Review → Verify → Revise) with no retry or repair mechanism. In the 2026-02-18 run, Phase 3 (Revise) had a 54% failure rate — 12 of 22 revisions failed with 'no revision output' (agent didn't write the expected file). These are infrastructure flakes, not content errors, but the script treats them as terminal.

Current flow:
  Review(all) → filter → Verify(candidates) → filter → Revise(confirmed) → done
                                                          ↑ 54% failure, no retry

Failed items are logged to events.jsonl and silently dropped. No feedback loop, no retry, no corrective action.

## Proposed Fix

Rearchitect using the **Check and Repair** pattern from the orchestrator skill (SKILL.md §3):

> The decision loop inherently handles repair. Each iteration the decision agent sees full current state — including failures, corrupt data, and partial results from the last action. If the last worker produced garbage, the decision agent sees it in context and corrects course.

### Specific changes:

1. **Wrap each phase in a retry-aware loop.** After the parallel fan-out completes, check for failures. Re-run failed items (up to N retries) before moving to the next phase. The retry should include the failure context (lastError, lastWorkerResult) so the agent can adjust.

2. **Use the decision loop pattern for Phase 3 (Revise).** This is where failures are most costly — verified findings that don't get revised are wasted work. The decision agent should see:
   - Which items succeeded and which failed
   - The error details (no output, wrong format, etc.)
   - Previous attempt count
   And decide: retry, skip, or escalate.

3. **Rebuild context fresh each iteration** (orchestrator principle 2). The decision agent should see the current state of the run directory — which files exist, which are missing, what the events.jsonl shows — not stale state from the start of the phase.

4. **Idempotency already works** — the script checks for existing output files before re-running. This means a retry loop just needs to re-invoke the same parallel fan-out and the existing items will be skipped.

### Pattern references:
- Check and Repair: orchestrator SKILL.md §3
- Decision loop with context rebuild: examples/development/lib/context.mld:44-51 (buildContext loads lastWorkerResult and lastError)
- Worker failure handling: examples/development/index.mld:164-171 (failure recorded in state, loop continues)
- Fan-out with retry: the existing for parallel() pattern already supports this — just wrap in a while loop that checks for incomplete items

### Evidence from 2026-02-18 run:
- Run dir: runs/review-docs-2026-02-18-1/
- 36/36 verifications succeeded
- 10/22 revisions succeeded, 12 failed ('no revision output')
- All failures are agent flakes (claude -p didn't write the file), not content errors
- events.jsonl shows 34 revise_failed events across runs (includes previous attempts)
