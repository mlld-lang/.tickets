---
id: m-0e6b
status: open
deps: [m-98a8]
links: []
created: 2026-02-09T06:03:26Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-9f2b
tags: [refactor, exec-invocation, phase-5, guards, policy]
---
# Phase 5: Extract guard and policy orchestration

Objective:
Extract guard and policy orchestration from core execution flow.

Instructions for the implementing agent:
- Move operation-context creation, policy capability checks, label-flow enforcement, and guard hook orchestration into `interpreter/eval/exec/guard-policy.ts` (or equivalent).
- Preserve interactions among pre-hook transforms, post-hook handling, retry signaling, and when-expression guard denial handling.
- Keep error types/messages and source-location behavior stable.

## Acceptance Criteria

1. Guard/policy orchestration is isolated in focused module(s).
2. Guard retry and policy denial paths preserve existing behavior.
3. Guard and policy test suites remain green.
4. Full test gate passes and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

