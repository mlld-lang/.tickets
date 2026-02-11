---
id: m-09cc
status: closed
deps: [m-0c66]
created: 2026-02-09T07:04:27Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-6937
tags: [refactor, ast-extractor, phase-6]
updated: 2026-02-11T19:43:13Z
---
# Refactor Program: Modularize interpreter/eval/ast-extractor.ts - Phase 6: Extract Solidity, Java, and C# extractor modules



Objective:
Modularize remaining language extractors.

Instructions for the implementing agent:
- Move Solidity, Java, and C# extractor implementations into dedicated modules using shared Definition contracts.
- Preserve member extraction behavior for class/interface/contract scopes.
- Add tests covering representative declarations for each language.

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

**2026-02-11 19:43 UTC:** --dir refactor Completed phase-6 extraction for Solidity/Java/C# language modules. Runtime modules touched: interpreter/eval/ast-extractor/solidity-extractor.ts (118 lines, owns contract/interface/library + member extraction and global Solidity declarations), interpreter/eval/ast-extractor/java-extractor.ts (92 lines, owns Java class/interface/enum and class-member extraction), interpreter/eval/ast-extractor/csharp-extractor.ts (188 lines, owns C# record/class/struct/interface/enum/method/constructor/variable extraction with attribute/modifier handling), interpreter/eval/ast-extractor.ts (77 lines, orchestrator/registry imports only after language-module extraction). Added phase-specific tests: interpreter/eval/ast-extractor/language-group-d.test.ts (representative declarations for Solidity/Java/C#). Targeted checks passed: npm run build && npx vitest run interpreter/eval/ast-extractor/language-group-d.test.ts interpreter/eval/ast-extractor/language-group-c.test.ts interpreter/eval/ast-extractor/language-group-b.test.ts interpreter/eval/ast-extractor/language-group-a.test.ts interpreter/eval/ast-extractor.characterization.test.ts interpreter/eval/ast-extractor/language-dispatch.test.ts interpreter/eval/content-loader-ast.test.ts interpreter/eval/alligator.structured.test.ts. Full gate passed: npm run build && npm test && npm run test:tokens && npm run test:examples. No new sub-60 runtime modules introduced. No callback-lambda service injection introduced.
