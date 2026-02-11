---
id: m-e66b
status: closed
deps: [m-b23a]
created: 2026-02-09T06:36:41Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-15ea
tags: [refactor, pipeline-executor, phase-1]
updated: 2026-02-11T05:27:56Z
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

1. Pure helpers and shared local types live in dedicated module(s) with stable imports.
2. `interpreter/eval/pipeline/executor.ts` compiles with behavior unchanged after extraction.
3. Exit criteria: all tests pass with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-11 05:26 UTC:** --dir refactor Phase 1 extraction implemented: created interpreter/eval/pipeline/executor/helpers.ts for pure helper primitives (parallel error formatting/context reset, serialization, stage value extraction, preview/snippet, descriptor lookup, clone) and interpreter/eval/pipeline/executor/types.ts for shared local executor types (RetrySignal/StageExecutionResult/ParallelStageError). Rewired interpreter/eval/pipeline/executor.ts to import extracted helpers/types with mechanical behavior-preserving moves only. Added focused helper tests in interpreter/eval/pipeline/executor/helpers.test.ts.\n\nTouched runtime modules + ownership:\n- interpreter/eval/pipeline/executor/helpers.ts (136 LOC): pure executor helper primitives\n- interpreter/eval/pipeline/executor.ts (1660 LOC): orchestrator using extracted helper/type modules\n- interpreter/eval/pipeline/executor/types.ts (21 LOC, type-only exception): shared local executor contracts\n\nTargeted verification:\n- npm run build (pass)\n- npx vitest run interpreter/eval/pipeline/executor/helpers.test.ts interpreter/eval/pipeline/executor.characterization.test.ts interpreter/eval/pipeline/command-execution.characterization.test.ts tests/pipeline/parallel-runtime.test.ts tests/interpreter/hooks/pipeline-context.test.ts tests/pipeline/data-handling.test.ts tests/pipeline/exec-structured.test.ts (pass)\n\nNo new sub-60 runtime module introduced (type-only exception: executor/types.ts).\nNo callback-lambda service injection introduced.

**2026-02-11 05:27 UTC:** --dir refactor Phase close full gate: npm run build && npm test && npm run test:tokens && npm run test:examples (pass).
