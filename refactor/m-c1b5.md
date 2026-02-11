---
id: m-c1b5
status: closed
deps: [m-7114]
created: 2026-02-09T07:03:52Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-ff50
tags: [refactor, show-evaluator, phase-3]
updated: 2026-02-11T18:12:59Z
---
# Refactor Program: Modularize interpreter/eval/show.ts - Phase 3: Extract path/template/load-content handlers



Objective:
Separate non-invocation content sourcing handlers.

Instructions for the implementing agent:
- Extract handlers for showPath, showPathSection, showTemplate, and showLoadContent branches into dedicated modules.
- Preserve section extraction/rename behavior and load-content integration behavior.
- Add tests for path/section/template/load-content flows and rename edge cases.

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

**2026-02-11 18:12 UTC:** --dir refactor
