---
id: m-ded6
status: open
deps: [m-33c8]
created: 2026-02-09T07:00:10Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-68f8
tags: [refactor, guard-post-hook, phase-5]
updated: 2026-02-09T07:00:10Z
---
# Refactor Program: Modularize interpreter/hooks/guard-post-hook.ts - Phase 5: Extract guard runtime evaluator and action handlers



Objective:
Modularize `evaluateGuard`, block execution, and replacement behavior.

Instructions for the implementing agent:
- In scope: runtime evaluator setup and action/block handler extraction for after-timing guards.
- Out of scope: retry enforcement builder logic and helper injection extraction.
- Extract guard runtime setup, context creation, and decision handling from `evaluateGuard` into dedicated evaluator modules.
- Extract `evaluateGuardBlock` and `evaluateGuardReplacement` while preserving action semantics and error locations.
- Add tests for allow/deny/retry behavior and replacement outcomes in after-timing context.

Module boundaries:
- Runtime evaluator module owns guard execution lifecycle.
- Action/block modules own block execution and replacement logic.
- Decision engine and retry enforcement remain external collaborators.

Preserve behavior checks:
- Allow/deny/retry action behavior remains unchanged.
- Replacement outcomes and transformed output behavior remain unchanged.
- Error locations/types for action/block evaluation remain unchanged.

## Acceptance Criteria

1. Runtime evaluator and action handlers are extracted into dedicated modules.
2. Action semantics, replacement behavior, and error surfaces remain baseline-compatible.
3. Tests cover after-timing allow/deny/retry and replacement flows.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
