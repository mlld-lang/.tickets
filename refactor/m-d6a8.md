---
id: m-d6a8
status: open
deps: [m-90f7]
created: 2026-02-09T07:06:06Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-722c
tags: [refactor, for-evaluator, phase-3]
updated: 2026-02-09T07:06:07Z
---
# Refactor Program: Modularize interpreter/eval/for.ts - Phase 3: Extract shared iteration runner core



Objective:
Share iteration loop orchestration across directive and expression paths.

Instructions for the implementing agent:
- Extract common run-one iteration setup/teardown logic (child env, context push/pop, binding setup, parallel isolation markers).
- Preserve ordering guarantees and behavior differences for ordered vs unordered concurrency where currently applied.
- Add tests for shared runner parity between directive and expression execution contexts.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
