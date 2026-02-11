---
id: m-cb8c
status: closed
deps: [m-9a8d]
links: []
created: 2026-02-09T06:24:52Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-d11b
tags: [refactor, var, phase-5, dispatcher]
updated: 2026-02-11T04:45:09Z
---
# Refactor Program: Modularize interpreter/eval/var.ts - Phase 5: Introduce typed RHS dispatcher orchestration

Objective:
Replace the monolithic RHS `if/else` chain with a typed dispatcher.

Instructions for the implementing agent:
- Introduce a typed RHS dispatcher module (example target: `interpreter/eval/var/rhs-dispatcher.ts`) that maps node shapes to evaluator handlers.
- Define a stable result contract for RHS evaluation (resolved value + branch metadata needed by later variable construction and pipeline phases).
- Keep dynamic imports localized to handler modules to avoid cycle/perf regressions.
- Preserve handler precedence and fallback semantics exactly.
- Add coverage for dispatcher routing to verify each supported node shape hits the intended handler.

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

1. `prepareVarAssignment` delegates RHS evaluation through a typed dispatcher.
2. Handler ordering and fallback behavior match baseline semantics.
3. Added tests verify routing coverage for supported RHS node shapes.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples


**2026-02-11 04:44 UTC:** Implemented Phase 5 typed RHS dispatcher orchestration.

Code changes:
- interpreter/eval/var/rhs-dispatcher.ts (450 LOC): typed handler-key dispatcher () with stable  contract for resolved values and branch metadata () plus special outcomes (, , ).
- interpreter/eval/var.ts (974 LOC):  delegates RHS branch routing to ; preserves precedence and downstream variable construction/pipeline flow.
- interpreter/eval/var/reference-evaluator.ts (174 LOC): reused for reference handler routing.
- interpreter/eval/var/execution-evaluator.ts (324 LOC): reused for execution handler routing.

Tests added:
- interpreter/eval/var/rhs-dispatcher.test.ts (routing coverage across content/primitive/literal/collection/reference/template/text/reference-tail/expression/execution/fallback + control-result paths)

Sub-checkpoint commands and status:
- npm run build ✅
- npm test -- interpreter/eval/var/rhs-dispatcher.test.ts interpreter/eval/var/reference-evaluator.test.ts interpreter/eval/var/execution-evaluator.test.ts interpreter/eval/var.characterization.test.ts ✅

Full gate command and status:
- npm run build && npm test && npm run test:tokens && npm run test:examples ✅

Guardrail assertions:
- No new runtime module under 60 LOC introduced (new runtime module: rhs-dispatcher.ts at 450 LOC).
- No callback-lambda service injection introduced; dispatcher composition uses typed dependency objects/services.
- Shared dispatcher result/context types are defined once in rhs-dispatcher.ts and imported/consumed from that contract.
- No new cyclic dependency introduced in interpreter/eval/var* (dispatcher depends on evaluator services; var.ts orchestrates and consumes result contract).

**2026-02-11 04:44 UTC:** Implemented Phase 5 typed RHS dispatcher orchestration.

Code changes:
- interpreter/eval/var/rhs-dispatcher.ts (450 LOC): typed handler-key dispatcher (`resolveHandlerKey`) with stable `RhsEvaluationResult` contract for resolved values and branch metadata (`handler`) plus special outcomes (`executable-variable`, `return-control`, `for-expression`).
- interpreter/eval/var.ts (974 LOC): `prepareVarAssignment` delegates RHS branch routing to `rhsDispatcher.evaluate(...)`; preserves precedence and downstream variable construction/pipeline flow.
- interpreter/eval/var/reference-evaluator.ts (174 LOC): reused for reference handler routing.
- interpreter/eval/var/execution-evaluator.ts (324 LOC): reused for execution handler routing.

Tests added:
- interpreter/eval/var/rhs-dispatcher.test.ts (routing coverage across content/primitive/literal/collection/reference/template/text/reference-tail/expression/execution/fallback + control-result paths)

Sub-checkpoint commands and status:
- npm run build ✅
- npm test -- interpreter/eval/var/rhs-dispatcher.test.ts interpreter/eval/var/reference-evaluator.test.ts interpreter/eval/var/execution-evaluator.test.ts interpreter/eval/var.characterization.test.ts ✅

Full gate command and status:
- npm run build && npm test && npm run test:tokens && npm run test:examples ✅

Guardrail assertions:
- No new runtime module under 60 LOC introduced (new runtime module: rhs-dispatcher.ts at 450 LOC).
- No callback-lambda service injection introduced; dispatcher composition uses typed dependency objects/services.
- Shared dispatcher result/context types are defined once in rhs-dispatcher.ts and imported/consumed from that contract.
- No new cyclic dependency introduced in interpreter/eval/var* (dispatcher depends on evaluator services; var.ts orchestrates and consumes result contract).
