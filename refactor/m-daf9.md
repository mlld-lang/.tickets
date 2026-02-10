---
id: m-daf9
status: open
deps: [m-f136]
created: 2026-02-09T07:03:18Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-04d8
tags: [refactor, exe-evaluator, phase-1]
updated: 2026-02-09T07:03:18Z
---
# Refactor Program: Modularize interpreter/eval/exe.ts - Phase 1: Extract shared parsing and definition helper utilities



Objective:
Separate generic helper logic from core eval orchestration.

Instructions for the implementing agent:
- Extract shared helpers for parameter extraction, description resolution, template-file parsing, interpolation recording, and loop-control detection.
- Keep parse fallbacks and current error behavior unchanged for template file parsing.
- Add tests for helper behavior and edge cases in parameter/type extraction.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
