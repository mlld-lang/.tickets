---
id: m-83af
status: closed
deps: [m-1b94]
created: 2026-02-09T06:58:06Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-c494
tags: [refactor, guard-pre-hook, phase-3]
updated: 2026-02-10T08:38:45Z
---
# Refactor Program: Modularize interpreter/hooks/guard-pre-hook.ts - Phase 3: Extract retry attempt state and decision accumulation


Objective:
Modularize attempt-store and decision-state mechanics.

Instructions for the implementing agent:
- In scope: retry attempt-store helpers and decision accumulation transitions only.
- Out of scope: guard candidate discovery and guard runtime execution internals.
- Extract root/attempt-store helpers (`getRootEnvironment`, `getAttemptStore`, attempt-key builders, clear helpers) into a retry-state module.
- Extract decision aggregation state transitions (allow/deny/retry/env + reasons/hints/metadata selection) into a dedicated reducer-like utility.
- Keep attempt-key identity rules stable across per-input vs per-operation scopes.
- Add tests for retry sequencing, store cleanup behavior, and mixed decision traces.

Module boundaries:
- Retry-state module owns attempt-key identity and store lifecycle.
- Decision reducer module owns transition/aggregation rules.
- Hook orchestrator composes both modules per guard run.

Preserve behavior checks:
- Retry-attempt isolation remains unchanged across per-input and per-operation scopes.
- Decision transition precedence remains unchanged across allow/deny/retry/env.
- Store cleanup behavior remains unchanged for success, deny, and abort flows.

## Acceptance Criteria

1. Attempt-store and decision-state logic are isolated from hook orchestration.
2. Retry history/state progression matches baseline behavior.
3. Cleanup semantics for successful/abort paths remain unchanged.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 08:38 UTC:** Phase 3 implementation complete.

What changed:
- Extracted retry-state ownership into `interpreter/hooks/guard-retry-state.ts`:
  - `getAttemptStore`, `buildGuardAttemptKey`, `clearGuardAttemptState`, `clearGuardAttemptStates`.
  - Attempt key identity remains rooted in operation trace/type/name + scope + variable identity.
- Extracted decision accumulation transitions into `interpreter/hooks/guard-decision-reducer.ts`:
  - reducer-style transition handling for `allow|deny|retry|env` traces,
  - first-env-config selection behavior,
  - cleanup/action mapping helpers.
- Updated `interpreter/hooks/guard-pre-hook.ts` to compose the new retry-state and reducer modules while preserving orchestration and runtime evaluation.
- Added targeted tests:
  - `tests/interpreter/hooks/guard-retry-state.test.ts` (retry store sequencing surface, key identity, cleanup helpers)
  - `tests/interpreter/hooks/guard-decision-reducer.test.ts` (mixed transition traces, retry-vs-deny precedence modes, cleanup/action mapping)

Checklist/acceptance coverage:
- [x] Attempt-store/key lifecycle isolated behind retry-state module.
- [x] Decision accumulation transitions isolated behind reducer utility.
- [x] Retry isolation rules preserved for per-input vs per-operation attempt keys.
- [x] Cleanup semantics preserved for continue/abort (clear) vs retry (retain).
- [x] Mixed decision trace precedence covered by targeted tests.

Tests run:
- npx vitest run tests/interpreter/hooks/guard-retry-state.test.ts tests/interpreter/hooks/guard-decision-reducer.test.ts tests/interpreter/hooks/guard-pre-hook.test.ts -> PASS
- npm run build && npm test && npm run test:tokens && npm run test:examples -> PASS
- npm run build && npm test && npm run test:tokens && npm run test:examples (phase completion rerun) -> PASS
