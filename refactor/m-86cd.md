---
id: m-86cd
status: closed
deps: [m-94d6]
created: 2026-02-09T07:00:44Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-21f6
tags: [refactor, when-evaluator, phase-4]
updated: 2026-02-11T16:06:11Z
---
# Refactor Program: Modularize interpreter/eval/when.ts - Phase 4: Extract matching engines (first/all/any/none)



Objective:
Modularize branch-selection algorithms.

Instructions for the implementing agent:
- Extract `evaluateFirstMatch`, `evaluateAllMatches`, `evaluateAnyMatch`, and none-placement validation into a matcher module.
- Preserve ordering semantics, none/wildcard constraints, and action execution behavior.
- Add tests for branch precedence and invalid-syntax error behavior.

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

**2026-02-11 16:06 UTC:** Completed phase objective: extracted matcher algorithms and none-placement validation into a dedicated matcher runtime module and rewired when form handlers to consume it via typed service composition.\n\nTouched runtime modules and ownership:\n- interpreter/eval/when/match-engines.ts (290 LOC): owns evaluateFirstMatch/evaluateAllMatches/evaluateAnyMatch plus none/wildcard validation and matcher runtime contract.\n- interpreter/eval/when/form-handlers.ts (241 LOC): owns simple/match/block form orchestration and delegates branch-selection semantics to matcher services.\n- interpreter/eval/when.ts (619 LOC): owns top-level composition/runtime assembly and shared condition/comparison/truthiness behavior.\n\nChecklist progress:\n- [x] Extracted evaluateFirstMatch/evaluateAllMatches/evaluateAnyMatch and none-placement validation into matcher module.\n- [x] Preserved ordering semantics, none/wildcard constraints, and action execution behavior.\n- [x] Added phase-specific tests for branch precedence and invalid-syntax behavior in interpreter/eval/when/match-engines.test.ts.\n\nTargeted test commands and status:\n- npm run build && npm test -- interpreter/eval/when/match-engines.test.ts interpreter/eval/when.characterization.test.ts interpreter/eval/when.test.ts interpreter/eval/for-expression-when.test.ts (pass)\n\nPhase close full gate and status:\n- npm run build && npm test && npm run test:tokens && npm run test:examples (pass)\n\nGuardrail attestations:\n- No new runtime module under 60 LOC introduced (match-engines.ts is 290 LOC).\n- No callback-lambda service injection introduced; used interface-typed runtime service objects.\n- No duplicated shared logic introduced across sibling modules in this phase scope.
