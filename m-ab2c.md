---
id: m-ab2c
status: closed
deps: [m-1c1f, m-30e4, m-180f]
created: 2026-02-19T19:06:43Z
type: task
priority: 0
assignee: Adam Avenir
updated: 2026-02-27T04:51:38Z
---
# Update orchestrators to use built-in checkpoint/resume instead of manual resumption

## Summary

mlld now has built-in checkpoint/resume support (llm labels + --checkpoint + --resume flags). All orchestrators in llm/run/ currently implement manual resumption: run directory resolution, file-existence-as-completion-markers, event logging for tracking progress, retry-loops-around-parallel-for, and CLI --run/--new flags. This manual machinery should be replaced by the built-in system, dramatically simplifying each orchestrator.

## What the new checkpoint/resume system provides

The spec is at: spec-hooks-checkpoints-resume.md (root of repo, 949 lines)
Contract doc: docs/dev/HOOKS-CHECKPOINT-RESUME-CONTRACT.md
Architecture: docs/dev/HOOKS.md

Key features:
- **llm label**: Any exe with the llm label (exe llm @fn(...) = ...) gets automatically cached to disk
- **--checkpoint flag**: mlld run script --checkpoint enables caching to .mlld/checkpoints/<script>/
- **--resume @fn**: Re-enter a script at a named function; everything before uses cache, from target forward re-executes
- **--resume @fn("partial-arg")**: Fuzzy matching for resume targets (e.g., match array items)
- **--fork source-script**: Start a new script inheriting another script's checkpoint cache
- **Cache key**: sha256(functionName + serializedArguments) — same call = cache hit
- **What's cached**: Only llm-labeled exe calls. Shell commands, file reads, variables all re-execute (they're cheap)

## What this replaces in each orchestrator

### The core pattern being eliminated

Every orchestrator currently does this:
1. Resolve or create a dated run directory (2026-02-19-0)
2. Before each LLM call, check if output file already exists (skip if yes)
3. After failures, wrap the parallel-for in a retry loop that checks for missing output files
4. Log events to JSONL for progress tracking and resumption context
5. Accept --run <id> and --new CLI flags for manual resume control

With checkpoints, this becomes:
1. Label LLM calls with llm: exe llm @review(...) = @claudePoll(...)
2. Run with --checkpoint
3. On crash/failure, re-run with --resume (or just re-run — cache hits are automatic)
4. No run directories, no file-existence checks, no retry loops, no --run/--new flags

### Orchestrator-by-orchestrator breakdown

#### review (LARGEST — ~200 lines of retry boilerplate)
File: llm/run/review/index.mld (547 lines)

Already has llm label on line 49: exe llm @llmCall(...) = @claudePoll(...)
Already has checkpoint hook on lines 54-57.

**Remove**: 5 identical retry-loop blocks (lines 139-180, 197-236, 253-292, 310-349, 420-473). Each is ~40 lines of: loop(@maxAttempts) [ for parallel... @fileExists check... missing items scan... continue ]. Replace with plain for parallel — checkpoint handles idempotency.

**Remove**: @fileExists checks inside each parallel item (lines 143-147, 201-205, 257-261, 314-318, 426-430)
**Remove**: @maxRetries/maxAttempts variables (lines 23-24)
**Simplify**: Run directory setup (lines 109-117) may still be useful for output organization but no longer needed for resumption

#### review-docs (Similar structure)
File: llm/run/review-docs/index.mld (354 lines)

**Remove**: 3-4 retry-loop blocks with same pattern as review
**Remove**: @fileExists idempotency checks in each phase
**Add**: llm label to LLM call wrapper

#### j2bd (Decision loop orchestrator)
File: llm/run/j2bd/index.mld (416 lines)

**Remove**: Run directory resolution (lines 46-79): @nextRunId, @mostRecentRun, @resolveRunId, @countRunsForDate
**Remove**: State persistence in lib/context.mld (lines 25-41): @loadRunState, @saveRunState
**Remove**: Event logging for iteration counting in lib/context.mld (lines 9-22): @loadRecentEvents, @countIterations
**Remove**: CLI flags --run, --new (lines 28-29)
**Remove**: Run initialization block (lines 86-98)
**Add**: llm label to @claudePoll calls
**Keep**: The decision loop itself — that's real logic, not resumption boilerplate

Note: j2bd's decision loop uses events.jsonl to count iterations and build context for the decision agent. Some event logging may still be useful for context-building even after checkpoint adoption. Evaluate what's purely for resumption vs. what's for decision-agent context.

#### qa (Multi-phase with sessions)
File: llm/run/qa/index.mld (217 lines)
State: llm/run/qa/lib/state.mld (130 lines)

**Remove**: Run resolution in state.mld (lines 6-28, 93-102): @mkdirp, @countRunsForDate, @nextRunId, @latestRunId, @resolveRunDir
**Remove**: Run state persistence in state.mld (lines 31-90): @createRun, @loadRun, @saveRun, @updatePhaseStatus, @updateRunStatus
**Remove**: Phase-completion tracking
**Add**: llm label to Claude session/poll calls
**Keep**: Phase logic (triage/flail/review), topic filtering

#### polish (Phase pipeline)
File: llm/run/polish/index.mld (287 lines)
State: llm/run/polish/lib/state.mld (169 lines)

**Remove**: Symlink-based run tracking (lines 72-104, 135)
**Remove**: State persistence in lib/state.mld (lines 50-133)
**Remove**: --run flag handling
**Add**: llm label to LLM calls
**Keep**: Phase ordering and --from/--to flags (these control what work to do, not resumption)

#### doc-audit
File: llm/run/doc-audit/index.mld (129 lines)

**Remove**: @fileExists idempotency checks (lines 51-56)
**Remove**: Dated run directory setup
**Add**: llm label to LLM call
Simplest orchestrator — minimal changes needed.

#### doc-writer
File: llm/run/doc-writer/index.mld (118 lines)

**Remove**: @fileExists idempotency checks
**Add**: llm label to LLM call
Also simple — minimal changes.

## Plugin examples to update

The same patterns exist in plugins/mlld/examples/:
- development/index.mld — run directory resolution, state persistence, event logging
- research/index.mld — run directory, state, events, filesystem-based phase inference
- audit/index.mld — @fileExists idempotency checks

These should be updated to demonstrate the checkpoint/resume pattern as the idiomatic approach.

## Implementation approach

1. Start with review/index.mld — it has the most boilerplate and already has the llm label
2. Remove retry-loop wrappers, replace with plain for parallel
3. Remove @fileExists idempotency checks
4. Test with --checkpoint flag to verify cache hits work
5. Move to other orchestrators
6. Update plugin examples last

## Getting up to speed

- Spec: spec-hooks-checkpoints-resume.md (root) — read the User Guide section (lines ~31-298) first
- Contract: docs/dev/HOOKS-CHECKPOINT-RESUME-CONTRACT.md
- Hooks architecture: docs/dev/HOOKS.md
- Risk gates: docs/dev/HOOKS-CHECKPOINT-RISK-GATES.md
- review orchestrator (already has llm label): llm/run/review/index.mld line 49
- Test fixtures for checkpoint behavior: tests/cases/ — search for "checkpoint" in fixture names


**2026-02-19 21:08 UTC:** ## Update: Named checkpoints replace custom phase flags

Depends on m-1c1f (named checkpoint directive). Once implemented, orchestrators should use checkpoint "name" directives between phases instead of custom --phase/--from/--to flags.

### review orchestrator
Replace `--phase 1|2|3|4|5|6` + `@shouldRunPhase()` with:
```mlld
checkpoint "spec-compliance"
>> ... phase 1 ...
checkpoint "test-quality"
>> ... phase 2 ...
checkpoint "docs-accuracy"
>> ... phase 3 ...
checkpoint "live-testing"
>> ... phase 4 ...
checkpoint "invalidation"
>> ... phase 5 ...
checkpoint "synthesis"
>> ... phase 6 ...
```
Then `mlld run review --resume "test-quality"` replaces `mlld run review --phase 2`.

### polish orchestrator
Replace `--from`/`--to` + phase ordering logic in lib/state.mld with checkpoint directives at phase boundaries. `mlld run polish --resume "execute"` replaces `mlld run polish --from execute`.

### qa orchestrator
Replace `--phase both|0|1|2` + conditional blocks with checkpoint directives. `mlld run qa --resume "flail"` replaces `mlld run qa --phase 1`.

### j2bd orchestrator
Single decision loop — no phase structure. Does NOT benefit from named checkpoints. Keep as-is for phase management.

## Update: Event log analysis

Events serve different purposes per orchestrator:

| Orchestrator | Events used as data? | Action |
|---|---|---|
| review | No — write-only audit trail | Remove event logging; checkpoint hooks handle observability |
| review-docs | No — write-only | Same |
| doc-audit | No — write-only | Same |
| doc-writer | No events at all | N/A |
| qa | No — agents consume results files, not events | Remove event logging |
| j2bd | YES — last 20 events passed to decision agent prompt | KEEP event logging for decision context; remove only iteration-counting guard (checkpoint handles crash recovery) |
| polish | YES — execute phase reads events for resumption (remove), merge phase reads events for branch/commit data (KEEP) | Split: remove resumption reads, keep merge workflow data reads |

## Update: Auto-checkpoint (m-30e4)

Once m-30e4 lands, --checkpoint flag is no longer needed. Scripts with llm labels auto-enable checkpointing. Remove --checkpoint from examples and docs.

## Dependencies

- m-1c1f: Named checkpoint directive (enables phase-level resume)
- m-30e4: Auto-enable checkpointing (removes --checkpoint ceremony)
- m-180f: @mlld/fs stdlib (removes shell helper boilerplate)
