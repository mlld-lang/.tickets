---
id: m-b18b
status: closed
deps: [m-c1b5]
created: 2026-02-09T07:03:52Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-ff50
tags: [refactor, show-evaluator, phase-4]
updated: 2026-02-11T18:20:31Z
---
# Refactor Program: Modularize interpreter/eval/show.ts - Phase 4: Extract invocation handlers



Objective:
Modularize executable/template invocation branches.

Instructions for the implementing agent:
- Extract unified invocation handlers (`addInvocation`/`showInvocation`, template invocation compatibility, exec invocation variants).
- Preserve method-call behavior and executable/non-executable error semantics.
- Add tests for invocation branch behavior and method-call resolution paths.

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

**2026-02-11 18:20 UTC:** --dir refactor
