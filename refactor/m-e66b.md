---
id: m-e66b
status: open
deps: [m-b23a]
created: 2026-02-09T06:36:41Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-15ea
tags: [refactor, pipeline-executor, phase-1]
updated: 2026-02-09T06:36:42Z
---
# Refactor Program: Modularize interpreter/eval/pipeline/executor.ts - Phase 1: Extract utilities and local types

Objective:
Extract shared executor utility primitives and local types into dedicated modules.

Instructions for the implementing agent:
- Create a focused module layout (for example, `interpreter/eval/pipeline/executor/`).
- Extract pure helpers and locally-scoped utility types that do not require class state, including serialization/preview/clone helpers and parallel error formatting helpers.
- Move stage/result type declarations into a dedicated types module where appropriate.
- Keep imports acyclic and avoid behavior changes; this phase is mechanical extraction.
- Add or update focused tests for extracted pure helpers where practical.

## Acceptance Criteria

1. Pure helpers and shared local types live in dedicated module(s) with stable imports.
2. `interpreter/eval/pipeline/executor.ts` compiles with behavior unchanged after extraction.
3. Exit criteria: all tests pass with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
