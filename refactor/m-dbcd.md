---
id: m-dbcd
status: closed
deps: [m-c82f]
created: 2026-02-09T07:00:44Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-21f6
tags: [refactor, when-evaluator, phase-6]
updated: 2026-02-11T16:24:20Z
---
# Refactor Program: Modularize interpreter/eval/when.ts - Phase 6: Extract comparison, truthiness, and denied-target utilities



Objective:
Isolate pure utilities used by condition runtime.

Instructions for the implementing agent:
- Extract `compareValues`, `isTruthy`, `conditionTargetsDenied`, and supporting literal/field helpers into a utility module.
- Keep existing coercion/truthiness semantics exactly aligned with baseline.
- Add targeted tests for string/boolean comparison edge cases and denied-target detection.

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

**2026-02-11 16:24 UTC:** Completed phase objective by extracting pure comparison/truthiness/denied-target utilities from when.ts.

Checklist completion:
- Extracted `compareValues`, `isTruthy`, `isDeniedLiteralNode`, and `conditionTargetsDenied` into `interpreter/eval/when/condition-utils.ts`.
- Preserved existing comparison and truthiness semantics by moving logic without behavior changes.
- Rewired `when.ts` and condition runtime composition to import utility functions from the new module.
- Added targeted phase coverage for string/boolean comparison edge cases and denied-target detection in `interpreter/eval/when/condition-utils.test.ts`.

Touched runtime modules (line counts + ownership):
- `interpreter/eval/when/condition-utils.ts` — 229 lines — owns denied-literal targeting, comparison coercion rules, and when truthiness semantics.
- `interpreter/eval/when.ts` — 102 lines — owns when orchestration and utility composition wiring.

Targeted validation:
- `npx vitest run interpreter/eval/when/condition-utils.test.ts interpreter/eval/when.characterization.test.ts interpreter/eval/when/match-engines.test.ts` ✅ pass

Full gate validation:
- `npm run build && npm test && npm run test:tokens && npm run test:examples` ✅ pass (final retry run)
- Additional stabilization check for transient timeout: `npx vitest run tests/streaming/guard-streaming.test.ts` ✅ pass

Guardrail attestations:
- No new sub-60 runtime module introduced.
- No callback-lambda service injection introduced; composition remains interface-typed service objects.
- No duplicated shared logic introduced across touched sibling modules.
