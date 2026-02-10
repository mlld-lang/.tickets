---
id: m-a5e7
status: open
deps: []
created: 2026-02-09T07:03:52Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-ff50
tags: [refactor, show-evaluator, phase-0]
updated: 2026-02-09T07:03:52Z
---
# Refactor Program: Modularize interpreter/eval/show.ts - Phase 0: Baseline and characterization



Objective:
Establish behavior baseline across show/add subtype handlers.

Instructions for the implementing agent:
- Map subtype branches and shared concerns: descriptor collection, materialization, policy checks, and effect emission.
- Add characterization tests covering showVariable, showPath/showPathSection, invocation variants, foreach variants, load-content, showCommand/showCode, and tail pipelines.
- Capture extraction seams and regression risks; do not move code across files in this phase.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
