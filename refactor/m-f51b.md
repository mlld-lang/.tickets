---
id: m-f51b
status: open
deps: [m-e66b]
created: 2026-02-09T06:36:41Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-15ea
tags: [refactor, pipeline-executor, phase-2]
updated: 2026-02-09T06:36:42Z
---
# Refactor Program: Modularize interpreter/eval/pipeline/executor.ts - Phase 2: Extract output normalization and descriptor propagation

Objective:
Isolate output normalization and security-descriptor propagation into a dedicated output module.

Instructions for the implementing agent:
- Extract normalization/finalization paths (`normalizeOutput`, descriptor merge/finalization helpers, source descriptor application) into focused collaborator(s).
- Extract stage-output cache concerns (`getStageOutput`, `getFinalOutput`, `clearStageOutputsFrom`) behind a narrow interface.
- Preserve descriptor merge precedence and provenance propagation behavior.
- Add targeted tests for normalization edge cases (null/primitive/object/array/structured input) and descriptor propagation.

## Acceptance Criteria

1. Output normalization/finalization and stage-output caching are isolated behind explicit module boundaries.
2. Descriptor and provenance behavior remains consistent with baseline tests.
3. Exit criteria: all tests pass with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
