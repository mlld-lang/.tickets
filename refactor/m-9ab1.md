---
id: m-9ab1
status: open
deps: [m-b258]
created: 2026-02-09T07:06:07Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-722c
tags: [refactor, for-evaluator, phase-5]
updated: 2026-02-09T07:06:07Z
---
# Refactor Program: Modularize interpreter/eval/for.ts - Phase 5: Extract expression evaluation and batch pipeline handler



Objective:
Isolate for-expression-specific result handling.

Instructions for the implementing agent:
- Extract expression-path sequence evaluation, control marker handling, and result normalization into dedicated modules.
- Extract batch pipeline post-processing and result variable construction logic.
- Add tests for for-expression controls (`skip`, `done`, return control) and batch pipeline behavior.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
