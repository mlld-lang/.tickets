---
id: m-d6a8
status: closed
deps: [m-90f7]
created: 2026-02-09T07:06:06Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-722c
tags: [refactor, for-evaluator, phase-3]
updated: 2026-02-11T20:14:45Z
---
# Refactor Program: Modularize interpreter/eval/for.ts - Phase 3: Extract shared iteration runner core



Objective:
Share iteration loop orchestration across directive and expression paths.

Instructions for the implementing agent:
- Extract common run-one iteration setup/teardown logic (child env, context push/pop, binding setup, parallel isolation markers).
- Preserve ordering guarantees and behavior differences for ordered vs unordered concurrency where currently applied.
- Add tests for shared runner parity between directive and expression execution contexts.

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

**2026-02-11 20:14 UTC:** Implemented shared iteration runner extraction for directive and expression paths.

Completed checklist items:
- Extracted common iteration setup/teardown (child env creation, parallel isolation marker, inherited __forOptions, binding setup, @mx.for context push/pop) into interpreter/eval/for/iteration-runner.ts.
- Rewired evaluateForDirective runOne and evaluateForExpression runOne to use the shared runner APIs.
- Added characterization parity coverage for directive vs expression @mx.for context values.

Touched runtime modules:
- interpreter/eval/for/iteration-runner.ts (140 LOC): shared iteration setup/teardown + expression context push/pop.
- interpreter/eval/for.ts (791 LOC): orchestrator updated to consume shared runner.

Targeted verification:
- npm run build ✅
- npx vitest run interpreter/eval/for.characterization.test.ts interpreter/eval/for-expression-mx.test.ts interpreter/eval/for-expression-when.test.ts interpreter/eval/for-nested-empty.test.ts tests/for/key-binding.test.ts tests/for/parallel-runtime.test.ts tests/integration/for-expr-error-propagation.test.ts tests/parallel/shell-commands-parallel.test.ts ✅

Phase full gate:
- npm run build && npm test && npm run test:tokens && npm run test:examples ✅

Guardrail checks:
- No new sub-60 runtime module introduced.
- No callback-lambda service injection introduced.
- Shared setup/teardown logic is centralized in one module; no duplicated sibling logic.
- Dependency direction remains acyclic in interpreter/eval/for* (for.ts -> for/iteration-runner.ts; no reverse import).
