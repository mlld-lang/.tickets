---
id: m-83af
status: open
deps: [m-1b94]
created: 2026-02-09T06:58:06Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-c494
tags: [refactor, guard-pre-hook, phase-3]
updated: 2026-02-09T06:58:21Z
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
