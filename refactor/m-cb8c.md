---
id: m-cb8c
status: open
deps: [m-9a8d]
links: []
created: 2026-02-09T06:24:52Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-d11b
tags: [refactor, var, phase-5, dispatcher]
---
# Refactor Program: Modularize interpreter/eval/var.ts - Phase 5: Introduce typed RHS dispatcher orchestration

Objective:
Replace the monolithic RHS `if/else` chain with a typed dispatcher.

Instructions for the implementing agent:
- Introduce a typed RHS dispatcher module (example target: `interpreter/eval/var/rhs-dispatcher.ts`) that maps node shapes to evaluator handlers.
- Define a stable result contract for RHS evaluation (resolved value + branch metadata needed by later variable construction and pipeline phases).
- Keep dynamic imports localized to handler modules to avoid cycle/perf regressions.
- Preserve handler precedence and fallback semantics exactly.
- Add coverage for dispatcher routing to verify each supported node shape hits the intended handler.

## Acceptance Criteria

1. `prepareVarAssignment` delegates RHS evaluation through a typed dispatcher.
2. Handler ordering and fallback behavior match baseline semantics.
3. Added tests verify routing coverage for supported RHS node shapes.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

