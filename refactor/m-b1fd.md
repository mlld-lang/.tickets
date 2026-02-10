---
id: m-b1fd
status: open
deps: [m-d0b8]
created: 2026-02-09T07:06:06Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-722c
tags: [refactor, for-evaluator, phase-1]
updated: 2026-02-09T07:06:06Z
---
# Refactor Program: Modularize interpreter/eval/for.ts - Phase 1: Extract parallel option and duration parsing services



Objective:
Isolate parallel option resolution logic.

Instructions for the implementing agent:
- Extract duration parsing/conversion and parallel cap/rate resolution helpers into a dedicated module.
- Preserve error messaging for invalid cap/rate values and variable-reference sourced options.
- Add tests for numeric/string/duration-node parallel option combinations.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
