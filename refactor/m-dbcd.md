---
id: m-dbcd
status: open
deps: [m-c82f]
created: 2026-02-09T07:00:44Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-21f6
tags: [refactor, when-evaluator, phase-6]
updated: 2026-02-09T07:00:44Z
---
# Refactor Program: Modularize interpreter/eval/when.ts - Phase 6: Extract comparison, truthiness, and denied-target utilities



Objective:
Isolate pure utilities used by condition runtime.

Instructions for the implementing agent:
- Extract `compareValues`, `isTruthy`, `conditionTargetsDenied`, and supporting literal/field helpers into a utility module.
- Keep existing coercion/truthiness semantics exactly aligned with baseline.
- Add targeted tests for string/boolean comparison edge cases and denied-target detection.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
