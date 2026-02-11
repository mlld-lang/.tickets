---
id: m-bd66
status: closed
deps: [m-15ea]
created: 2026-02-09T06:42:54Z
type: epic
priority: 1
assignee: Adam Avenir
tags: [refactor, run]
updated: 2026-02-11T07:45:55Z
---
# Refactor Program: Modularize interpreter/eval/run.ts

Objective:
Break interpreter/eval/run.ts into focused modules with stable interfaces while preserving runtime semantics, policy enforcement, security descriptor flow, streaming behavior, and output effects.

Scope:
- Keep behavior stable across all /run subtypes (runCommand, runCode, runExec, runExecInvocation, runExecReference, runPipeline).
- Preserve operation-context updates, policy checks, taint/descriptor propagation, environment provider execution, and effect emission semantics.
- Preserve pipeline + streaming interactions and final display/output behavior.

Phase plan:
- Phase 0: Baseline and characterization.
- Phase 1: Extract pure helpers and pre-extracted run input readers.
- Phase 2: Extract operation-context and policy/taint guard helpers.
- Phase 3: Extract run-command execution orchestration.
- Phase 4: Extract direct run-code execution path.
- Phase 5: Extract executable reference resolution.
- Phase 6: Extract executable definition handler dispatcher.
- Phase 7: Extract pipeline, streaming, and output-finalization lifecycle.
- Phase 8: Final composition cleanup and architecture verification.

Linked phase tickets:
- Phase 0: m-f706
- Phase 1: m-1f33
- Phase 2: m-e62f
- Phase 3: m-43a7
- Phase 4: m-3887
- Phase 5: m-ba71
- Phase 6: m-4313
- Phase 7: m-4a76
- Phase 8: m-a3d9

Cross-phase constraints:
- Keep `evaluateRun` as the external entrypoint and preserve subtypes: runCommand, runCode, runExec, runExecInvocation, runExecReference, runPipeline.
- Preserve operation-context label/source metadata updates and policy/label-flow checks across command/code/executable branches.
- Preserve stage-0 retry eligibility, stream-format override behavior, and effect emission gating semantics.
- Keep diffs surgical and backed by characterization tests before structural moves.

Execution rule:
Each phase runs and passes the full test gate before the next phase starts.

## Wave 2 Architecture Contract

Target end state for `interpreter/eval/run.ts`:
- `evaluateRun` remains the external entrypoint and becomes a thin orchestrator.
- Distinct modules own command flow, code flow, executable reference resolution, executable definition dispatch, policy/operation-context helpers, and pipeline/stream/output finalization.
- Pre-extracted input readers and pure helper utilities are centralized and reused by branches.
- Shared run contracts/types are defined once and imported across run modules.

Wave 2 guardrails for every phase in this epic:
- Do not create runtime modules under 60 lines unless the file is a pure type-definition file or re-export barrel.
- Do not duplicate branch-specific policy/descriptor logic; shared helpers are mandatory.
- Do not compose branch handlers using constructor callback-lambda bundles.
- Verify no circular dependencies inside `interpreter/eval/run*` after each phase.

## Minimum Targeted Test Evidence

Every phase note includes targeted coverage from suites that exercise this area, using files such as:
- `interpreter/eval/run.structured.test.ts`
- `interpreter/eval/run-in-blocks.test.ts`
- `tests/cases/feat/strict-mode-run-command/*`
- `tests/cases/feat/strict-mode-run-exec/*`

## Acceptance Criteria

1. All phase tickets exist and are ordered by dependencies.
2. Each phase ticket includes explicit implementation instructions and clear acceptance criteria.
3. Exit criteria for every phase requires the full test gate to pass before the next phase starts.
4. Final exit criteria: full test gate passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-11 07:45 UTC:** Wave 2 epic closeout architecture checkpoint

Final composition and responsibilities:
- evaluateRun entrypoint: interpreter/eval/run.ts (355 LOC) acts as orchestration hub for /run,/sh subtypes.
- Branch ownership modules:
  - run-command-executor.ts (455 LOC): runCommand orchestration, interpolation, stdin/auth/policy wiring.
  - run-code-executor.ts (157 LOC): direct runCode execution and policy/context integration.
  - run-exec-resolver.ts (239 LOC): executable reference resolution (field-chain, serialized refs, call-stack handoff).
  - run-exec-definition-dispatcher.ts (699 LOC): executable-definition dispatch (command/ref/transformer/code/template/prose) and runExec argument extraction.
  - run-output-lifecycle.ts (161 LOC): with-clause pipeline application, streaming finalization, output/effect finalization.
- Shared support modules:
  - run-policy-context.ts (164 LOC): operation-context and policy/taint utilities.
  - run-pre-extracted-inputs.ts (74 LOC): pre-extracted variable/descriptor readers.
  - run-pure-helpers.ts (102 LOC): pure shared helpers for run flows.

Cycle-break seam note:
- Local run evaluator import graph is acyclic with one-way direction: run.ts -> specialized modules -> leaf helpers.
- No module in run-modules imports run.ts; orchestration boundary remains stable.

Module count and line distribution:
- Runtime files in this area: 9 total (run.ts + 8 non-test run-modules files).
- run-modules non-test total: 2051 LOC.
- Smallest runtime module in area: 74 LOC.

Guardrail confirmations:
- No new sub-60 runtime module introduced.
- No thin facade module introduced; each runtime module owns concrete branch behavior/policy/lifecycle logic.
- No callback-lambda service injection introduced.

Final gate evidence:
- Required full gate command completed successfully: npm run build && npm test && npm run test:tokens && npm run test:examples (exit 0).

