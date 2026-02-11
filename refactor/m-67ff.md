---
id: m-67ff
status: closed
deps: [m-6f75]
created: 2026-02-09T07:04:26Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-6937
tags: [refactor, ast-extractor, phase-3]
updated: 2026-02-11T18:59:45Z
---
# Refactor Program: Modularize interpreter/eval/ast-extractor.ts - Phase 3: Extract TS, Python, and Ruby extractor modules



Objective:
Modularize first language extractor group.

Instructions for the implementing agent:
- Move TS, Python, and Ruby extractor implementations into dedicated modules with a stable Definition contract.
- Preserve nested member detection behavior and extracted code/search slices.
- Add language-specific tests for representative definitions and usage extraction cases.

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

**2026-02-11 18:59 UTC:** --dir refactor Completed phase-3 extraction for TS/Python/Ruby definitions. Runtime modules touched: interpreter/eval/ast-extractor/typescript-extractor.ts (78 lines, owns TypeScript AST statement/member extraction and Definition shaping), interpreter/eval/ast-extractor/python-extractor.ts (87 lines, owns Python top-level/class/method/variable extraction and indentation block slicing), interpreter/eval/ast-extractor/ruby-extractor.ts (132 lines, owns Ruby module/class/function/method/constant extraction with context-qualified names), interpreter/eval/ast-extractor.ts (797 lines, orchestrator/registry wiring updated to import extracted modules and keep remaining language extractors). Added phase-specific tests: interpreter/eval/ast-extractor/language-group-a.test.ts (TS/Python/Ruby representative definitions + usage extraction). Targeted checks passed: npm run build && npx vitest run interpreter/eval/ast-extractor/language-group-a.test.ts interpreter/eval/ast-extractor.characterization.test.ts interpreter/eval/ast-extractor/language-dispatch.test.ts interpreter/eval/content-loader-ast.test.ts interpreter/eval/alligator.structured.test.ts. Full gate passed: npm run build && npm test && npm run test:tokens && npm run test:examples. No new sub-60 runtime modules introduced. No callback-lambda service injection introduced.
