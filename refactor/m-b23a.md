---
id: m-b23a
status: closed
deps: [m-e412]
created: 2026-02-09T06:36:41Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-15ea
tags: [refactor, pipeline-executor, phase-0]
updated: 2026-02-11T05:21:50Z
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

1. Characterization tests cover the listed high-risk paths.
2. No intentional semantic changes land in this phase.
3. Exit criteria: all tests pass with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-11 05:20 UTC:** --dir refactor Phase 0 characterization coverage added in interpreter/eval/pipeline/executor.characterization.test.ts (9 tests) and tightened leading-|| shorthand syntax in tests/pipeline/parallel-runtime.test.ts. Added explicit tests for: synthetic __source__ first-run + retry source replay; retry signal parsing for 'retry' and { value:'retry', hint, from }; inline command policy capability denial and label-flow enforcement on stdin taint; while-stage processor callback path; parallel-stage ordered aggregation + error-marker payloads; streaming lifecycle events (PIPELINE_START/STAGE_* / PIPELINE_COMPLETE and abort path).\n\nChecklist status: all listed high-risk paths mapped to explicit tests; no runtime behavior refactor performed.\n\nTouched runtime modules with line counts: none (test-only phase).\nNo new runtime module under 60 lines introduced.\nNo callback-lambda service injection introduced.\n\nTargeted verification:\n- npm run build (pass)\n- npx vitest run interpreter/eval/pipeline/executor.characterization.test.ts (pass)\n- npx vitest run tests/interpreter/hooks/pipeline-context.test.ts tests/pipeline/parallel-runtime.test.ts tests/pipeline/data-handling.test.ts tests/pipeline/exec-structured.test.ts interpreter/eval/pipeline/command-execution.characterization.test.ts interpreter/eval/pipeline/executor.characterization.test.ts (pass)

**2026-02-11 05:21 UTC:** --dir refactor Phase close full gate: npm run build && npm test && npm run test:tokens && npm run test:examples (pass).
