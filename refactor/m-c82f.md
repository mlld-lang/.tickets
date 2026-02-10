---
id: m-c82f
status: open
deps: [m-86cd]
created: 2026-02-09T07:00:44Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-21f6
tags: [refactor, when-evaluator, phase-5]
updated: 2026-02-09T07:00:44Z
---
# Refactor Program: Modularize interpreter/eval/when.ts - Phase 5: Extract condition evaluation pipeline



Objective:
Separate condition-expression runtime from form orchestration.

Instructions for the implementing agent:
- Extract `evaluateCondition` branches for unary wrappers, unified expressions, exec invocation conditions, and generic evaluation paths.
- Preserve resolution-context behavior and diagnostics wrapping (`MlldConditionError`).
- Add tests for denied literal handling, expression evaluation errors, and exec invocation truthiness.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
