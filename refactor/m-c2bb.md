---
id: m-c2bb
status: closed
deps: []
links: []
created: 2026-02-09T06:03:26Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-9f2b
tags: [refactor, exec-invocation, phase-0]
updated: 2026-02-10T18:50:02Z
---
# Phase 0: Baseline and characterization coverage for exec invocation

Objective:
Establish a stable behavioral baseline before refactoring.

Instructions for the implementing agent:
- Identify high-risk behaviors in `interpreter/eval/exec-invocation.ts` and current direct tests.
- Add or tighten characterization tests for:
  - builtin method/object invocation chains
  - with-clause pipeline behavior on exec invocation
  - autoverify injection path
  - env var injection and referenced-parameter filtering
  - command-ref argument pass-through and security label preservation
  - retryable source behavior for invocation-level pipelines
- Avoid structural refactors in this phase; focus on safety net.
- Capture baseline notes in this ticket.

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

1. Characterization tests cover the identified high-risk paths and pass.
2. Existing behavior remains unchanged (no intentional semantic changes).
3. Full test gate passes and output is attached to this ticket:
   npm run build && npm test && npm run test:tokens && npm run test:examples


**2026-02-10 18:49 UTC:** Phase 0 characterization coverage completed (no runtime refactor changes).

What changed:
- Added 4 characterization tests in interpreter/eval/exec-invocation.structured.test.ts.
  1) Builtin/object invocation chain via objectSource recursion (trim -> split -> join).
  2) Command-ref explicit argument forwarding preserves structured security metadata when secure structured args are passed.
  3) Command-ref pass-through (no commandArgs) preserves structured security metadata when secure structured args are passed.
  4) Invocation-level with.pipeline source remains retryable (pipeline.try reaches 3).
- Existing direct suites already cover with-clause pipeline behavior, autoverify injection path, and env var referenced-parameter filtering.

Checklist coverage status:
- builtin method/object invocation chains: complete
- with-clause pipeline behavior on exec invocation: complete (existing + retained coverage)
- autoverify injection path: complete (existing direct suite)
- env var injection + referenced-parameter filtering: complete (existing direct suite)
- command-ref argument pass-through + security metadata preservation: complete
- retryable source behavior for invocation-level pipelines: complete

Touched runtime modules (line counts + ownership):
- None (Phase 0 is test-only characterization work).

Touched test modules:
- interpreter/eval/exec-invocation.structured.test.ts (452 LOC): expanded direct characterization safety net for high-risk exec invocation paths.

Wave 2 guardrail confirmations:
- No new runtime modules created; no sub-60 runtime module introduced.
- No callback-lambda service injection introduced.
- No shared type duplication introduced in runtime area.
- No dependency graph change in runtime area (acyclic direction unchanged).

Targeted validation run:
- npx vitest run interpreter/eval/exec-invocation.structured.test.ts interpreter/eval/exec-invocation.env-injection.test.ts interpreter/eval/exec-invocation.autoverify.test.ts (pass)
- npx vitest run tests/pipeline-context-retry.test.ts interpreter/methods.split.test.ts (pass)
- npm run build (pass)

Full gate run (phase close requirement):
- npm run build && npm test && npm run test:tokens && npm run test:examples (pass)
