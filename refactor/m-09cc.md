---
id: m-09cc
status: open
deps: [m-0c66]
created: 2026-02-09T07:04:27Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-6937
tags: [refactor, ast-extractor, phase-6]
updated: 2026-02-09T07:04:27Z
---
# Refactor Program: Modularize interpreter/eval/ast-extractor.ts - Phase 6: Extract Solidity, Java, and C# extractor modules



Objective:
Modularize remaining language extractors.

Instructions for the implementing agent:
- Move Solidity, Java, and C# extractor implementations into dedicated modules using shared Definition contracts.
- Preserve member extraction behavior for class/interface/contract scopes.
- Add tests covering representative declarations for each language.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
