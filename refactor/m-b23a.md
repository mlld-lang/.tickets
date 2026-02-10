---
id: m-b23a
status: open
deps: []
created: 2026-02-09T06:36:41Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-15ea
tags: [refactor, pipeline-executor, phase-0]
updated: 2026-02-09T06:36:41Z
---
# Refactor Program: Modularize interpreter/eval/pipeline/executor.ts - Phase 0: Baseline and characterization

Objective:
Establish characterization coverage for high-risk behavior in `PipelineExecutor` before structural changes.

Instructions for the implementing agent:
- Identify high-risk paths and map each one to explicit tests.
- Add or tighten characterization tests for:
  - synthetic `__source__` first execution and retry behavior
  - retry signal parsing (`retry`, `{ value: 'retry' }`, retry hints/scopes)
  - inline command stage policy checks and label-flow enforcement
  - while-stage processor execution path
  - parallel-stage ordered aggregation and error-marker behavior
  - streaming lifecycle events (`PIPELINE_START`, stage events, `PIPELINE_COMPLETE`/abort)
- Keep this phase focused on tests and safety-net improvements; avoid architectural refactors.

## Acceptance Criteria

1. Characterization tests cover the listed high-risk paths.
2. No intentional semantic changes land in this phase.
3. Exit criteria: all tests pass with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
