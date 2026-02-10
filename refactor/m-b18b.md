---
id: m-b18b
status: open
deps: [m-c1b5]
created: 2026-02-09T07:03:52Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-ff50
tags: [refactor, show-evaluator, phase-4]
updated: 2026-02-09T07:03:52Z
---
# Refactor Program: Modularize interpreter/eval/show.ts - Phase 4: Extract invocation handlers



Objective:
Modularize executable/template invocation branches.

Instructions for the implementing agent:
- Extract unified invocation handlers (`addInvocation`/`showInvocation`, template invocation compatibility, exec invocation variants).
- Preserve method-call behavior and executable/non-executable error semantics.
- Add tests for invocation branch behavior and method-call resolution paths.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
