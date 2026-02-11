---
id: m-03eb
status: closed
deps: [m-7976]
created: 2026-02-09T07:00:43Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-21f6
tags: [refactor, when-evaluator, phase-1]
updated: 2026-02-11T15:46:30Z
---
# Refactor Program: Modularize interpreter/eval/when.ts - Phase 1: Extract assignment and variable ownership utilities



Objective:
Separate assignment value computation and ownership checks from when form logic.

Instructions for the implementing agent:
- Extract `evaluateAssignmentValue`, ownership lookup helpers, and isolation-root checks into a dedicated assignment support module.
- Keep let redefinition and += mutation guard behavior unchanged, including source-location rich errors.
- Add tests for shadowing rules, imported/block-scoped exceptions, and isolation-root mutation denial.

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

**2026-02-11 15:46 UTC:** Phase 1 complete: extracted assignment and ownership/isolation helpers from when evaluator.\n\nRuntime modules touched:\n- interpreter/eval/when/assignment-support.ts (267 LOC): owns assignment value evaluation, let/+= evaluation, variable owner lookup, isolation root checks.\n- interpreter/eval/when.ts (1281 LOC): now delegates assignment paths to assignment-support while preserving exports and call sites.\n\nTest modules touched:\n- interpreter/eval/when.characterization.test.ts (425 LOC): added coverage for let shadowing guard paths (default deny, block-scoped/imported/context overrides) and parallel isolation-root += mutation denial/allow behavior.\n\nTargeted checks:\n- npm run build && npm test -- interpreter/eval/when.characterization.test.ts interpreter/eval/when.test.ts interpreter/eval/for-expression-when.test.ts (pass)\n\nFull gate evidence:\n- npm run build && npm test (pass)\n- npm run test:tokens && npm run test:examples (pass)\n\nGuardrail confirmations:\n- No new sub-60 runtime module introduced (new runtime module is 267 LOC).\n- No callback-lambda service injection introduced.
