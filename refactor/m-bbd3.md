---
id: m-bbd3
status: open
deps: [m-9408]
created: 2026-02-09T07:03:52Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-ff50
tags: [refactor, show-evaluator, phase-6]
updated: 2026-02-09T07:03:53Z
---
# Refactor Program: Modularize interpreter/eval/show.ts - Phase 6: Extract show-command and show-code handlers



Objective:
Isolate runtime execution handlers used by show.

Instructions for the implementing agent:
- Extract showCommand/showCode execution logic, including inline helper logic for raw code extraction/dedent.
- Preserve execution context wiring and output handling semantics.
- Add tests for command/code execution display behavior and error propagation.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
