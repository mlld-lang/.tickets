---
id: m-ef95
status: open
deps: [m-9ab1]
created: 2026-02-09T07:06:07Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-722c
tags: [refactor, for-evaluator, phase-6]
updated: 2026-02-09T07:06:07Z
---
# Refactor Program: Modularize interpreter/eval/for.ts - Phase 6: Extract error aggregation and context reporting helpers



Objective:
Centralize for-loop error/context reporting utilities.

Instructions for the implementing agent:
- Extract error formatting, context reset, and for-context snapshot helpers into dedicated modules.
- Preserve logging and stderr effect behavior for parallel iteration failures.
- Add tests for forErrors context publication and formatted error output behavior.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
