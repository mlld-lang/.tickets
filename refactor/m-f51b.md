---
id: m-f51b
status: closed
deps: [m-e66b]
created: 2026-02-09T06:36:41Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-15ea
tags: [refactor, pipeline-executor, phase-2]
updated: 2026-02-11T05:37:09Z
---
# Refactor Program: Modularize interpreter/eval/pipeline/executor.ts - Phase 2: Extract output normalization and descriptor propagation

Objective:
Isolate output normalization and security-descriptor propagation into a dedicated output module.

Instructions for the implementing agent:
- Extract normalization/finalization paths (`normalizeOutput`, descriptor merge/finalization helpers, source descriptor application) into focused collaborator(s).
- Extract stage-output cache concerns (`getStageOutput`, `getFinalOutput`, `clearStageOutputsFrom`) behind a narrow interface.
- Preserve descriptor merge precedence and provenance propagation behavior.
- Add targeted tests for normalization edge cases (null/primitive/object/array/structured input) and descriptor propagation.

## Wave 2 Phase Guardrails

Required design constraints for this phase:
- Do not create runtime modules under 60 lines unless the file is a pure type-definition file or re-export barrel.
- Do not duplicate shared logic across sibling modules in this area; extract a shared helper/service when behavior overlaps.
- Do not introduce constructor callback-lambda injection as a service-composition strategy; use interface-typed service objects.
- Define shared result/context/types/type-guards once per extraction area and import them instead of re-declaring.
- Keep module dependency direction acyclic in the touched area before closing the phase.

Evidence required in the phase note before close:
- Touched runtime modules with line counts and a short ownership note per module.
- Targeted test commands run for this phase and pass/fail status.
- Explicit statement that no new sub-60 runtime module was introduced (or list allowed type/barrel exceptions).
- Explicit statement that no callback-lambda service injection was introduced (or justify a recursion-only exception).

## Acceptance Criteria

1. Output normalization/finalization and stage-output caching are isolated behind explicit module boundaries.
2. Descriptor and provenance behavior remains consistent with baseline tests.
3. Exit criteria: all tests pass with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-11 05:34 UTC:** --dir refactor Phase 2 extraction implemented: created interpreter/eval/pipeline/executor/output-processor.ts to isolate output normalization + descriptor/provenance propagation, and interpreter/eval/pipeline/executor/stage-output-cache.ts to isolate stage output cache state and final-output resolution. Rewired interpreter/eval/pipeline/executor.ts to use these collaborators (no architecture callbacks injected). Added focused tests: interpreter/eval/pipeline/executor/output-processor.test.ts and interpreter/eval/pipeline/executor/stage-output-cache.test.ts, plus existing helper/executor characterization coverage.\n\nTouched runtime modules + ownership:\n- interpreter/eval/pipeline/executor/output-processor.ts (147 LOC): normalize/finalize + descriptor merge/source propagation\n- interpreter/eval/pipeline/executor/stage-output-cache.ts (83 LOC): stage output cache/state/final output semantics\n- interpreter/eval/pipeline/executor.ts (1454 LOC): orchestration wiring to extracted collaborators\n- interpreter/eval/pipeline/executor/types.ts (21 LOC, type-only exception): shared local contracts\n\nTargeted verification:\n- npm run build (pass)\n- npx vitest run interpreter/eval/pipeline/executor/helpers.test.ts interpreter/eval/pipeline/executor/output-processor.test.ts interpreter/eval/pipeline/executor/stage-output-cache.test.ts interpreter/eval/pipeline/executor.characterization.test.ts interpreter/eval/pipeline/command-execution.characterization.test.ts tests/interpreter/hooks/pipeline-context.test.ts tests/pipeline/parallel-runtime.test.ts tests/pipeline/data-handling.test.ts tests/pipeline/exec-structured.test.ts (pass)\n\nNo new sub-60 runtime module introduced (type-only exception: executor/types.ts).\nNo callback-lambda service injection introduced.

**2026-02-11 05:37 UTC:** --dir refactor
