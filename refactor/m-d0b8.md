---
id: m-d0b8
status: closed
deps: [m-51a5]
created: 2026-02-09T07:06:06Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-722c
tags: [refactor, for-evaluator, phase-0]
updated: 2026-02-11T19:55:09Z
---
# Refactor Program: Modularize interpreter/eval/for.ts - Phase 0: Baseline and characterization



Objective:
Establish baseline coverage for directive/expression iteration flows.

Instructions for the implementing agent:
- Map shared and divergent responsibilities between directive and expression paths, including parallel behavior and control-flow handling.
- Add characterization tests for field-path binding, key variable rules, parallel cap/rate behavior, and control markers.
- Capture extraction seams and regression risks; do not move code across files in this phase.

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

**2026-02-11 19:54 UTC:** --dir refactor Phase 0 characterization completed without runtime extraction.\n\nWhat changed:\n- Added for evaluator characterization coverage at interpreter/eval/for.characterization.test.ts (222 lines).\n- Added baseline cases for dotted field-path binding, missing-field error context, key-variable field rejection, parallel cap/rate option resolution (including invalid-value diagnostics), and continue/done control markers for directive and expression paths.\n\nTouched runtime modules and ownership:\n- interpreter/eval/for.ts (1314 lines): unchanged in this phase; remains the single runtime owner for directive/expression iteration semantics pending extraction phases.\n\nChecklist/evidence status:\n- Characterization map for directive/expression overlap captured through explicit tests around shared binding/parallel/control behaviors.\n- Regression-risk seams captured by asserting current error strings and loop control outcomes.\n\nTargeted verification (pass):\n- npm run build\n- npx vitest run interpreter/eval/for.characterization.test.ts interpreter/eval/for-expression-mx.test.ts interpreter/eval/for-expression-when.test.ts interpreter/eval/for-nested-empty.test.ts tests/for/key-binding.test.ts tests/for/parallel-runtime.test.ts tests/integration/for-expr-error-propagation.test.ts\n\nPhase exit gate (pass):\n- npm run build && npm test && npm run test:tokens && npm run test:examples\n\nGuardrail confirmations:\n- No new runtime module introduced in this phase (test-only addition).\n- No sub-60 runtime module introduced.\n- No callback-lambda service injection introduced.\n- No new cross-module dependency edges introduced; acyclic direction unchanged.
