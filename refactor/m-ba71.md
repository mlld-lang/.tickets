---
id: m-ba71
status: closed
deps: [m-3887]
created: 2026-02-09T06:44:04Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-bd66
tags: [refactor, run, phase-5]
updated: 2026-02-11T07:23:14Z
---
# Refactor Program: Modularize interpreter/eval/run.ts - Phase 5: Extract executable reference resolution

Objective:
Separate executable reference lookup and field-chain traversal from execution handling.

Instructions for the implementing agent:
- Extract logic that resolves runExec identifiers into executable variables, including:
  - simple executable lookup
  - field access traversal
  - transformer variant selection
  - serialized executable rehydration
  - string reference dereference
- Preserve current error semantics for missing variables, invalid field access, and non-executable resolution.
- Preserve call-stack/cycle detection behavior.
- Provide an explicit resolver API that returns executable definition context used by downstream handlers.
- Add focused tests for successful and failing resolution paths.

## Wave 2 Phase Guardrails

Required design constraints for this phase:
- Do not create runtime modules under 60 lines unless the file is a pure type-definition file or re-export barrel.
- Do not duplicate shared logic across sibling modules in this area; extract a shared helper/service when behavior overlaps.
- Do not introduce constructor callback-lambda injection as a service-composition strategy; use interface-typed service objects.
- Define shared result/context/types/type-guards once per extraction area and import them instead of re-declaring.
- Keep module dependency direction acyclic in the touched area before closing the phase.

Evidence required in the phase note before close:
- Touched runtime modules with line counts and a short ownership note per module.
- Targeted test commands run for this phase and pass/fail status.
- Explicit statement that no new sub-60 runtime module was introduced (or list allowed type/barrel exceptions).
- Explicit statement that no callback-lambda service injection was introduced (or justify a recursion-only exception).

## Acceptance Criteria

1. runExec reference resolution lives in a dedicated resolver module.
2. Error behavior and circular-reference detection remain consistent.
3. Focused resolver tests pass for success and failure cases.
4. Exit criteria (required before next phase): full test gate passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples


**2026-02-11 07:23 UTC:** --dir refactor Phase 5 implementation complete.

Runtime modules touched:
- interpreter/eval/run-modules/run-exec-resolver.ts (239 LOC): owns runExec identifier resolution (simple executable lookup, field-chain traversal, transformer variant selection, serialized executable rehydration, string-reference dereference, and executable definition/context return).
- interpreter/eval/run.ts (877 LOC): runExec branch now consumes resolver API and preserves downstream execution/cycle-check behavior.

Tests added/updated:
- interpreter/eval/run-modules/run-exec-resolver.test.ts (244 LOC): focused success/failure coverage for simple resolution, variant resolution, serialized rehydration, string-reference resolution, missing variant, and missing definition errors.

Sub-checkpoint evidence:
- npm run build && npx vitest run interpreter/eval/run-modules/run-exec-resolver.test.ts interpreter/eval/run.characterization.test.ts interpreter/eval/run.structured.test.ts
- Result: PASS (Test Files 3 passed; Tests 36 passed, 1 skipped).

Phase full-gate evidence:
- npm run build && npm test && npm run test:tokens && npm run test:examples
- Result: PASS
  - npm test: Test Files 338 passed, 7 skipped; Tests 3803 passed, 63 skipped.
  - npm run test:tokens: Test Files 6 passed; Tests 331 passed, 6 skipped.
  - npm run test:examples: Test Files 1 passed; Tests 1353 passed, 2 skipped.

Guardrail checks:
- No new runtime module under 60 lines (new runtime resolver is 239 LOC).
- No callback-lambda service injection introduced.
- Shared runExec resolution logic is consolidated in one runtime module; no duplicated sibling logic introduced.
- Dependency direction in the touched run module area remains acyclic.
