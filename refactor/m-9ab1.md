---
id: m-9ab1
status: closed
deps: [m-b258]
created: 2026-02-09T07:06:07Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-722c
tags: [refactor, for-evaluator, phase-5]
updated: 2026-02-11T20:28:07Z
---
# Refactor Program: Modularize interpreter/eval/for.ts - Phase 5: Extract expression evaluation and batch pipeline handler



Objective:
Isolate for-expression-specific result handling.

Instructions for the implementing agent:
- Extract expression-path sequence evaluation, control marker handling, and result normalization into dedicated modules.
- Extract batch pipeline post-processing and result variable construction logic.
- Add tests for for-expression controls (`skip`, `done`, return control) and batch pipeline behavior.

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

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-11 20:27 UTC:** Extracted for-expression iteration evaluation and batch pipeline handling into dedicated modules.

Completed checklist items:
- Extracted expression-path sequence evaluation (let/augmented/when/general evaluation ordering) and control marker handling into interpreter/eval/for/expression-runner.ts.
- Extracted expression result normalization (skip/done handling, structured/variable normalization, JSON-string parse behavior, descriptor propagation to object results) into interpreter/eval/for/expression-runner.ts.
- Extracted batch pipeline post-processing and fallback behavior into interpreter/eval/for/batch-pipeline.ts.
- Introduced shared ForIterationError type contract in interpreter/eval/for/types.ts (pure type file).
- Added characterization coverage for batch pipeline success and fallback-on-failure behavior.

Touched runtime modules:
- interpreter/eval/for/expression-runner.ts (237 LOC): expression sequence runtime + control/result normalization.
- interpreter/eval/for/batch-pipeline.ts (89 LOC): batch pipeline execution, normalization, fallback/error marker behavior.
- interpreter/eval/for.ts (468 LOC): orchestration updated to consume extracted expression runtime and batch pipeline modules.

Touched type module:
- interpreter/eval/for/types.ts (7 LOC, type-only exception): shared ForIterationError contract.

Targeted verification:
- npm run build ✅
- npx vitest run interpreter/eval/for.characterization.test.ts interpreter/eval/for-expression-mx.test.ts interpreter/eval/for-expression-when.test.ts interpreter/eval/for-nested-empty.test.ts tests/for/key-binding.test.ts tests/for/parallel-runtime.test.ts tests/for/rate-limit-retry.test.ts tests/integration/for-expr-error-propagation.test.ts tests/parallel/shell-commands-parallel.test.ts ✅

Phase full gate:
- npm run build && npm test && npm run test:tokens && npm run test:examples ✅

Guardrail checks:
- No new sub-60 runtime module introduced.
- No callback-lambda service injection introduced.
- Shared expression evaluation and batch handling logic centralized; no duplicated sibling logic.
- Dependency direction remains acyclic in interpreter/eval/for* (for.ts -> expression-runner/batch-pipeline/types; no reverse import).
