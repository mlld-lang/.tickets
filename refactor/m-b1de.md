---
id: m-b1de
status: open
deps: [m-e379]
links: []
created: 2026-02-09T06:03:26Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-9f2b
tags: [refactor, exec-invocation, phase-3, builtins]
---
# Phase 3: Extract builtin and object invocation flow

Objective:
Extract builtin/object invocation flow and reduce duplicated with-clause/pipeline handling.

Instructions for the implementing agent:
- Move builtin method dispatch and object source/reference resolution into `interpreter/eval/exec/builtins.ts`.
- Preserve behavior for string/array/search/type-check builtins and post-field access.
- Introduce shared helper(s) for applying pipeline + with-clause handling to avoid repeated blocks.
- Keep error messages and edge-case behavior consistent.

## Acceptance Criteria

1. Builtin/object invocation logic lives in focused module(s).
2. Duplicated pipeline/with-clause handling in builtin branches is reduced via shared helper(s).
3. Builtin-related tests (including split/indexing/pipeline flows) remain green.
4. Full test gate passes and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

