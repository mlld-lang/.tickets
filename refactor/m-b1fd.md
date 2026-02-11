---
id: m-b1fd
status: closed
deps: [m-d0b8]
created: 2026-02-09T07:06:06Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-722c
tags: [refactor, for-evaluator, phase-1]
updated: 2026-02-11T19:59:44Z
---
# Refactor Program: Modularize interpreter/eval/for.ts - Phase 1: Extract parallel option and duration parsing services



Objective:
Isolate parallel option resolution logic.

Instructions for the implementing agent:
- Extract duration parsing/conversion and parallel cap/rate resolution helpers into a dedicated module.
- Preserve error messaging for invalid cap/rate values and variable-reference sourced options.
- Add tests for numeric/string/duration-node parallel option combinations.

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

**2026-02-11 19:59 UTC:** --dir refactor Phase 1 completed: extracted parallel option and duration parsing services.\n\nWhat changed:\n- Extracted parallel option resolution into interpreter/eval/for/parallel-options.ts (143 lines).\n- Removed inline parallel parsing/resolution helpers from interpreter/eval/for.ts and wired orchestration to import resolveParallelOptions/type ForParallelOptions.\n- Expanded characterization tests in interpreter/eval/for.characterization.test.ts for numeric, string, and duration-node combinations.\n\nTouched runtime modules and ownership:\n- interpreter/eval/for/parallel-options.ts (143 lines): owns duration parsing, cap/rate normalization, variable-reference resolution, and error messaging for invalid cap/rate values.\n- interpreter/eval/for.ts (1185 lines): remains orchestrator/loop runtime owner; now delegates parallel option normalization to parallel-options module.\n\nPhase-specific tests added/updated:\n- Added for-expression coverage for:\n  - numeric cap + duration-literal pacing\n  - string cap/rate from variable references\n  - duration-node pacing object sourced through variable reference\n\nTargeted verification (pass):\n- npm run build\n- npx vitest run interpreter/eval/for.characterization.test.ts interpreter/eval/for-expression-mx.test.ts interpreter/eval/for-expression-when.test.ts interpreter/eval/for-nested-empty.test.ts tests/for/key-binding.test.ts tests/for/parallel-runtime.test.ts tests/integration/for-expr-error-propagation.test.ts\n\nPhase exit gate (pass):\n- npm run build && npm test && npm run test:tokens && npm run test:examples\n\nGuardrail confirmations:\n- No sub-60 runtime module introduced (new runtime module is 143 lines).\n- No callback-lambda service injection introduced.\n- Shared parallel option types are defined once in parallel-options.ts and imported by for.ts.\n- Dependency direction remains acyclic in interpreter/eval/for* touched area.
