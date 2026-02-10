---
id: m-ba71
status: open
deps: [m-3887]
created: 2026-02-09T06:44:04Z
type: task
priority: 1
assignee: Adam Avenir
updated: 2026-02-09T06:44:11Z
---
# Refactor Program: Modularize interpreter/eval/run.ts - Phase 5: Extract executable reference resolution

Objective:
Separate executable reference lookup and field-chain traversal from execution handling.

Instructions for the implementing agent:
- Extract logic that resolves runExec identifiers into executable variables, including:
  - simple executable lookup
  - field access traversal
  - transformer variant selection
  - serialized executable rehydration
  - string reference dereference
- Preserve current error semantics for missing variables, invalid field access, and non-executable resolution.
- Preserve call-stack/cycle detection behavior.
- Provide an explicit resolver API that returns executable definition context used by downstream handlers.
- Add focused tests for successful and failing resolution paths.

## Acceptance Criteria

1. runExec reference resolution lives in a dedicated resolver module.
2. Error behavior and circular-reference detection remain consistent.
3. Focused resolver tests pass for success and failure cases.
4. Exit criteria (required before next phase): full test gate passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

