---
id: m-c2bb
status: open
deps: []
links: []
created: 2026-02-09T06:03:26Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-9f2b
tags: [refactor, exec-invocation, phase-0]
---
# Phase 0: Baseline and characterization coverage for exec invocation

Objective:
Establish a stable behavioral baseline before refactoring.

Instructions for the implementing agent:
- Identify high-risk behaviors in `interpreter/eval/exec-invocation.ts` and current direct tests.
- Add or tighten characterization tests for:
  - builtin method/object invocation chains
  - with-clause pipeline behavior on exec invocation
  - autoverify injection path
  - env var injection and referenced-parameter filtering
  - command-ref argument pass-through and security label preservation
  - retryable source behavior for invocation-level pipelines
- Avoid structural refactors in this phase; focus on safety net.
- Capture baseline notes in this ticket.

## Acceptance Criteria

1. Characterization tests cover the identified high-risk paths and pass.
2. Existing behavior remains unchanged (no intentional semantic changes).
3. Full test gate passes and output is attached to this ticket:
   npm run build && npm test && npm run test:tokens && npm run test:examples

