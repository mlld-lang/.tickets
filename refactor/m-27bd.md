---
id: m-27bd
status: closed
deps: [m-899d]
created: 2026-02-09T07:03:19Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-04d8
tags: [refactor, exe-evaluator, phase-5]
updated: 2026-02-11T17:12:28Z
---
# Refactor Program: Modularize interpreter/eval/exe.ts - Phase 5: Extract executable definition builders (control-flow subtype families)



Objective:
Separate control-flow expression subtype builders.

Instructions for the implementing agent:
- Extract builders for when/foreach/for/loop/exeBlock subtype branches and special language markers.
- Preserve validation checks on AST node type expectations and error messaging.
- Add tests for each control-flow subtype and invalid-content failures.

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

**2026-02-11 17:12 UTC:** Extracted control-flow executable definition builders into interpreter/eval/exe/control-flow-definition-builders.ts and replaced inline control-flow subtype branching in interpreter/eval/exe.ts with a dedicated builder dispatch.

Touched runtime modules:
- interpreter/eval/exe/control-flow-definition-builders.ts (165 LOC): owns when/foreach/for/loop/exeBlock definition construction, special language markers, and control-flow content validation errors.
- interpreter/eval/exe.ts (233 LOC): owns evaluateExe orchestration and delegates core/control-flow subtype families.

Phase checklist coverage:
- Extracted builders for when/foreach/for/loop/exeBlock subtype families.
- Preserved AST node validation checks and user-facing error messages for invalid content.
- Added focused coverage in interpreter/eval/exe/control-flow-definition-builders.test.ts for all control-flow subtype definitions plus invalid-content failures.

Validation:
- npm run build && npx vitest run interpreter/eval/exe/control-flow-definition-builders.test.ts interpreter/eval/exe.characterization.test.ts interpreter/eval/exe/block-execution.test.ts interpreter/eval/when.characterization.test.ts (PASS)
- npm run build && npm test && npm run test:tokens && npm run test:examples (PASS)

Guardrails:
- No new runtime module under 60 LOC was introduced.
- No callback-lambda service injection was introduced.
- Dependency direction in interpreter/eval/exe* remains acyclic for touched modules.
