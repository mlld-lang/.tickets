---
id: m-daf9
status: closed
deps: [m-f136]
created: 2026-02-09T07:03:18Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-04d8
tags: [refactor, exe-evaluator, phase-1]
updated: 2026-02-11T16:48:06Z
---
# Refactor Program: Modularize interpreter/eval/exe.ts - Phase 1: Extract shared parsing and definition helper utilities



Objective:
Separate generic helper logic from core eval orchestration.

Instructions for the implementing agent:
- Extract shared helpers for parameter extraction, description resolution, template-file parsing, interpolation recording, and loop-control detection.
- Keep parse fallbacks and current error behavior unchanged for template file parsing.
- Add tests for helper behavior and edge cases in parameter/type extraction.

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

**2026-02-11 16:47 UTC:** Phase completion evidence:\n- Scope implemented: extracted shared parsing/definition helper utilities from interpreter/eval/exe.ts into interpreter/eval/exe/definition-helpers.ts and rewired exe orchestration to use shared helpers.\n- Touched runtime modules with line counts + ownership:\n  - interpreter/eval/exe.ts (1338 LOC): remains orchestration for exec subtype routing, executable variable construction, and wrapper execution paths; now consumes shared helper services.\n  - interpreter/eval/exe/definition-helpers.ts (157 LOC): owns parameter name/type extraction, description resolution, interpolation+security recording, loop-control detection, and template-file parsing fallback/error semantics.\n- Test coverage added/updated:\n  - interpreter/eval/exe/definition-helpers.test.ts (158 LOC) covering helper behavior and parameter/type edge cases.\n- Targeted tests run (pass):\n  - npm run build\n  - npx vitest run interpreter/eval/exe/definition-helpers.test.ts interpreter/eval/exe.characterization.test.ts interpreter/import-template-detection.test.ts\n- Full gate run (pass):\n  - npm run build && npm test && npm run test:tokens && npm run test:examples\n- Guardrails:\n  - No new runtime module under 60 LOC introduced.\n  - No callback-lambda service injection introduced.\n  - Shared helper types/logic are defined once in extracted helper module for this slice.\n  - Dependency direction remains acyclic in touched area (exe.ts -> exe/definition-helpers.ts).
