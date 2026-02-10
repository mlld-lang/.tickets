---
id: m-03eb
status: open
deps: [m-7976]
created: 2026-02-09T07:00:43Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-21f6
tags: [refactor, when-evaluator, phase-1]
updated: 2026-02-09T07:00:43Z
---
# Refactor Program: Modularize interpreter/eval/when.ts - Phase 1: Extract assignment and variable ownership utilities



Objective:
Separate assignment value computation and ownership checks from when form logic.

Instructions for the implementing agent:
- Extract `evaluateAssignmentValue`, ownership lookup helpers, and isolation-root checks into a dedicated assignment support module.
- Keep let redefinition and += mutation guard behavior unchanged, including source-location rich errors.
- Add tests for shadowing rules, imported/block-scoped exceptions, and isolation-root mutation denial.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
