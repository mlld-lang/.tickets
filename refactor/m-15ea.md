---
id: m-15ea
status: closed
deps: [m-d11b]
created: 2026-02-09T06:36:41Z
type: epic
priority: 1
assignee: Adam Avenir
tags: [refactor, pipeline-executor]
updated: 2026-02-11T06:19:41Z
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

## Wave 2 Architecture Contract

Target end state for `interpreter/eval/pipeline/executor.ts`:
- `PipelineExecutor` keeps its external execute contracts while becoming orchestration/composition focused.
- Stage execution seams are explicit: command resolution/binding, inline+while adapters, single-stage runner, parallel-stage orchestrator, streaming lifecycle, descriptor normalization/merge, and retry utilities.
- Synthetic `__source__`/`__identity__` semantics, stage ordering, and retry constraints stay unchanged.
- Shared executor contracts/types are defined once for stage collaborators.

Wave 2 guardrails for every phase in this epic:
- Do not create runtime modules under 60 lines unless the file is a pure type-definition file or re-export barrel.
- Do not duplicate stage orchestration logic across single/parallel paths; shared behavior is centralized.
- Do not wire stage collaborators via callback-lambda constructor bundles.
- Verify no circular dependencies in `interpreter/eval/pipeline/executor*` after each phase.

## Minimum Targeted Test Evidence

Every phase note includes targeted coverage from suites that exercise this area, using files such as:
- `tests/interpreter/hooks/pipeline-context.test.ts`
- `tests/pipeline/parallel-runtime.test.ts`
- `tests/pipeline/data-handling.test.ts`
- `tests/pipeline/exec-structured.test.ts`
- `interpreter/eval/pipeline/command-execution.characterization.test.ts`

## Acceptance Criteria

1. Phase tickets 0-8 exist and map to distinct responsibilities in `interpreter/eval/pipeline/executor.ts`.
2. Phase tickets close in sequence with implementation/test evidence attached.
3. Final architecture splits executor responsibilities into cohesive modules under `interpreter/eval/pipeline/executor/`.
4. Final exit criteria: all tests pass with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples


**2026-02-09 06:36 UTC:** Phase chain: m-b23a -> m-e66b -> m-f51b -> m-a092 -> m-e217 -> m-2042 -> m-c639 -> m-69d1 -> m-3bb8

**2026-02-11 06:19 UTC:** --dir refactor Executor epic architecture summary (m-15ea)

Final module set and responsibilities:
- interpreter/eval/pipeline/executor.ts (508 LOC): composition root with stable execute overloads; wires stage runners, command services, cache, output processor, execution loop runner, and streaming lifecycle.
- interpreter/eval/pipeline/executor/single-stage-runner.ts (356 LOC): single-stage orchestration, stage-env/context boundaries, retry handling, output finalization, and effect ordering.
- interpreter/eval/pipeline/executor/parallel-stage-runner.ts (332 LOC): parallel branch execution, ordered aggregation, error-marker handling, retry-signal rejection, and branch descriptor merge.
- interpreter/eval/pipeline/executor/execution-loop-runner.ts (201 LOC): state-machine-driven execute loop and final-state outcome handling.
- interpreter/eval/pipeline/executor/streaming-lifecycle.ts (87 LOC): stage-streaming predicates, stream event emission, setup, and teardown.
- interpreter/eval/pipeline/executor/inline-stage-executor.ts (159 LOC): inline value/inline command stage execution paths.
- interpreter/eval/pipeline/executor/output-processor.ts (147 LOC): normalization/finalization and descriptor/source propagation.
- interpreter/eval/pipeline/executor/helpers.ts (136 LOC): shared structured/serialization/error helper surface.
- interpreter/eval/pipeline/executor/command-argument-binder.ts (105 LOC): argument binding for command invocation.
- interpreter/eval/pipeline/executor/while-stage-adapter.ts (111 LOC): while-processor to pipeline-command adaptation.
- interpreter/eval/pipeline/executor/stage-output-cache.ts (83 LOC): stage output cache and retry-clear behavior.
- interpreter/eval/pipeline/executor/command-invoker.ts (76 LOC): command invocation bridge.
- interpreter/eval/pipeline/executor/types.ts (38 LOC): shared type-definition file.

Callback/cycle-break seams:
- No callback-lambda constructor bundles are introduced for service composition.
- Collaborators compose via interface-typed runtime/service objects.
- No recursion-only cycle-break callback seam is required in this extraction area.

Module count and line-count distribution:
- Runtime/composition modules in executor area: 12 non-type files plus 1 shared type-definition file.
- Large orchestration modules: executor.ts (508), single-stage-runner.ts (356), parallel-stage-runner.ts (332), execution-loop-runner.ts (201).
- Mid-size modules: inline-stage-executor.ts (159), output-processor.ts (147), helpers.ts (136), while-stage-adapter.ts (111), command-argument-binder.ts (105), streaming-lifecycle.ts (87), stage-output-cache.ts (83), command-invoker.ts (76).

Guardrail confirmations for epic end state:
- No runtime module under 60 lines (exception: types.ts as pure type-definition file).
- No thin facade/forwarder modules; each runtime module owns material logic and behavior.
- No duplicated logic across sibling modules in this extraction area.
- Local executor import graph is acyclic (executor.ts + executor/*.ts cycle check result: NO_LOCAL_CYCLES).

Epic full gate evidence:
- npm run build && npm test && npm run test:tokens && npm run test:examples
- Status: PASS at final phase close boundary.

Residual risks and follow-up opportunities:
- Repo-wide circular dependencies remain outside interpreter/eval/pipeline/executor* and are unchanged by this epic; a separate cross-area dependency cleanup can address those independently.
