---
id: m-c1b5
status: open
deps: [m-7114]
created: 2026-02-09T07:03:52Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-ff50
tags: [refactor, show-evaluator, phase-3]
updated: 2026-02-09T07:03:52Z
---
# Refactor Program: Modularize interpreter/eval/show.ts - Phase 3: Extract path/template/load-content handlers



Objective:
Separate non-invocation content sourcing handlers.

Instructions for the implementing agent:
- Extract handlers for showPath, showPathSection, showTemplate, and showLoadContent branches into dedicated modules.
- Preserve section extraction/rename behavior and load-content integration behavior.
- Add tests for path/section/template/load-content flows and rename edge cases.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
