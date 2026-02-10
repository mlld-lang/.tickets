---
id: m-67ff
status: open
deps: [m-6f75]
created: 2026-02-09T07:04:26Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-6937
tags: [refactor, ast-extractor, phase-3]
updated: 2026-02-09T07:04:26Z
---
# Refactor Program: Modularize interpreter/eval/ast-extractor.ts - Phase 3: Extract TS, Python, and Ruby extractor modules



Objective:
Modularize first language extractor group.

Instructions for the implementing agent:
- Move TS, Python, and Ruby extractor implementations into dedicated modules with a stable Definition contract.
- Preserve nested member detection behavior and extracted code/search slices.
- Add language-specific tests for representative definitions and usage extraction cases.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
