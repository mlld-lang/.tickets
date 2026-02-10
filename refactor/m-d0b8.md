---
id: m-d0b8
status: open
deps: []
created: 2026-02-09T07:06:06Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-722c
tags: [refactor, for-evaluator, phase-0]
updated: 2026-02-09T07:06:06Z
---
# Refactor Program: Modularize interpreter/eval/for.ts - Phase 0: Baseline and characterization



Objective:
Establish baseline coverage for directive/expression iteration flows.

Instructions for the implementing agent:
- Map shared and divergent responsibilities between directive and expression paths, including parallel behavior and control-flow handling.
- Add characterization tests for field-path binding, key variable rules, parallel cap/rate behavior, and control markers.
- Capture extraction seams and regression risks; do not move code across files in this phase.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
