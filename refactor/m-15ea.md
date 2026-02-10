---
id: m-15ea
status: open
deps: []
created: 2026-02-09T06:36:41Z
type: epic
priority: 1
assignee: Adam Avenir
tags: [refactor, pipeline-executor]
updated: 2026-02-09T06:36:41Z
---
# Refactor Program: Modularize interpreter/eval/pipeline/executor.ts

Largest unplanned interpreter implementation file: `interpreter/eval/pipeline/executor.ts` (~1,765 LOC).

Objective:
Decompose `PipelineExecutor` into focused modules while preserving behavior for stage execution, retry semantics, streaming, security descriptor propagation, and parallel group handling.

Execution order:
1. Complete Phase 0 through Phase 8 in strict sequence.
2. Close each phase only after meeting its exit criteria and attaching evidence.
3. Keep external behavior stable; semantic changes are out of scope.

Cross-phase constraints:
- Keep `PipelineExecutor.execute` overload signatures and external call sites stable.
- Preserve behavior for synthetic `__source__` and `__identity__` stages.
- Preserve label/descriptor propagation and policy/guard enforcement.
- Preserve parallel-stage ordering and retry constraints.

## Acceptance Criteria

1. Phase tickets 0-8 exist and map to distinct responsibilities in `interpreter/eval/pipeline/executor.ts`.
2. Phase tickets close in sequence with implementation/test evidence attached.
3. Final architecture splits executor responsibilities into cohesive modules under `interpreter/eval/pipeline/executor/`.
4. Final exit criteria: all tests pass with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples


**2026-02-09 06:36 UTC:** Phase chain: m-b23a -> m-e66b -> m-f51b -> m-a092 -> m-e217 -> m-2042 -> m-c639 -> m-69d1 -> m-3bb8
