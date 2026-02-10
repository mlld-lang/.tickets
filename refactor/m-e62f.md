---
id: m-e62f
status: open
deps: [m-1f33]
created: 2026-02-09T06:44:04Z
type: task
priority: 1
assignee: Adam Avenir
updated: 2026-02-09T06:44:10Z
---
# Refactor Program: Modularize interpreter/eval/run.ts - Phase 2: Extract operation-context and policy guard utilities

Objective:
Isolate policy and operation-context logic so command, code, and executable paths share a consistent guard pipeline.

Instructions for the implementing agent:
- Extract helpers that build and apply operation context (labels, sources, metadata) for command and capability operations.
- Extract policy check helpers for command access and capability access, preserving existing error types and codes.
- Extract label-flow guard helpers for arg/stdin/using channels and output-policy descriptor derivation.
- Centralize descriptor-to-taint conversion call sites so run branches use shared utilities.
- Ensure helper contracts are explicit and easy to unit test.
- Add tests that verify deny/allow behavior and channel-specific label-flow checks remain unchanged.

## Acceptance Criteria

1. Operation-context and policy/taint checks are extracted into dedicated utilities and reused across branches.
2. Error type/code/message behavior for denied operations remains consistent.
3. New tests cover command/capability denial and label-flow channel behavior.
4. Exit criteria (required before next phase): full test gate passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

