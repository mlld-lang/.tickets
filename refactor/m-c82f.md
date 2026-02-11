---
id: m-c82f
status: closed
deps: [m-86cd]
created: 2026-02-09T07:00:44Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-21f6
tags: [refactor, when-evaluator, phase-5]
updated: 2026-02-11T16:16:47Z
---
# Refactor Program: Modularize interpreter/eval/when.ts - Phase 5: Extract condition evaluation pipeline



Objective:
Separate condition-expression runtime from form orchestration.

Instructions for the implementing agent:
- Extract `evaluateCondition` branches for unary wrappers, unified expressions, exec invocation conditions, and generic evaluation paths.
- Preserve resolution-context behavior and diagnostics wrapping (`MlldConditionError`).
- Add tests for denied literal handling, expression evaluation errors, and exec invocation truthiness.

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

**2026-02-11 16:16 UTC:** Completed phase objective by extracting condition-evaluation runtime out of when.ts into a dedicated module.

Checklist completion:
- Extracted branches for unary wrappers, denied literal handling, unified expression conditions, exec invocation conditions, and generic evaluate() path into `interpreter/eval/when/condition-evaluator.ts`.
- Preserved `MlldConditionError` wrapping and resolution-context behavior (truthiness/equality resolution boundaries are unchanged).
- Added phase coverage in `interpreter/eval/when.characterization.test.ts` for denied literal handling and expression-error wrapping, while retaining exec invocation truthiness coverage.

Touched runtime modules (line counts + ownership):
- `interpreter/eval/when/condition-evaluator.ts` — 244 lines — owns condition-path orchestration and error wrapping for when conditions.
- `interpreter/eval/when.ts` — 349 lines — owns when form orchestration and composes condition runtime via service object.

Targeted validation:
- `npx vitest run interpreter/eval/when.characterization.test.ts interpreter/eval/when/match-engines.test.ts` ✅ pass

Full gate validation:
- `npm run build && npm test && npm run test:tokens && npm run test:examples` ✅ pass

Guardrail attestations:
- No new sub-60 runtime module introduced.
- No callback-lambda service injection introduced; composition uses interface-typed runtime service objects.
- No duplicated shared logic introduced across touched sibling modules.
