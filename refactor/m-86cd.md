---
id: m-86cd
status: open
deps: [m-94d6]
created: 2026-02-09T07:00:44Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-21f6
tags: [refactor, when-evaluator, phase-4]
updated: 2026-02-09T07:00:44Z
---
# Refactor Program: Modularize interpreter/eval/when.ts - Phase 4: Extract matching engines (first/all/any/none)



Objective:
Modularize branch-selection algorithms.

Instructions for the implementing agent:
- Extract `evaluateFirstMatch`, `evaluateAllMatches`, `evaluateAnyMatch`, and none-placement validation into a matcher module.
- Preserve ordering semantics, none/wildcard constraints, and action execution behavior.
- Add tests for branch precedence and invalid-syntax error behavior.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
