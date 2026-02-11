---
id: m-a69c
status: closed
deps: [m-c97c]
created: 2026-02-09T07:02:44Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-845d
tags: [refactor, pipeline-command-execution, phase-5]
updated: 2026-02-10T11:12:52Z
---
# Refactor Program: Modularize interpreter/eval/pipeline/command-execution.ts - Phase 5: Extract command/provider execution handler



Objective:
Modularize command and provider execution handling while preserving retry and descriptor behavior.

Instructions for the implementing agent:
- In scope: extract the command execution branch, including provider dispatch and with-clause pipeline chaining.
- Out of scope: non-command branch handlers (code/node/template/ref), reference resolution internals, and preflight extraction.
- Move command/provider handler to `.../handlers/execute-command.ts` (or equivalent), with explicit inputs for normalized executable, bound params, preflight context, and runtime deps.
- Preserve provider/non-provider parity for interpolation, using/auth injection, and runtime invocation contracts.
- Add tests for provider and direct command execution, nested with-clause pipeline chaining, descriptor merges, and retry signal propagation.

Module boundaries:
- Command handler executes only command/provider branches and returns normalized execution result contracts.
- Retry signaling and descriptor merge behavior are exposed as explicit handler outputs.
- Handler does not own guard/policy preflight decisions.

Preserve behavior checks:
- Provider path and non-provider path produce equivalent metadata contracts where baseline expects parity.
- Retry signals from nested pipelines preserve shape and propagation semantics.
- Output descriptor merge behavior remains stable.
- Command branch error surface stays baseline-compatible.

## Acceptance Criteria

1. Command/provider execution logic is extracted into a dedicated handler with explicit interfaces.
2. Retry propagation and descriptor merge behavior remain unchanged relative to characterization tests.
3. Preserve-behavior checks are asserted for provider and non-provider paths.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 11:11 UTC:** Implemented phase slice for command/provider handler extraction.

What changed:
- Extracted command/provider execution branch to `interpreter/eval/pipeline/command-execution/handlers/execute-command.ts`.
- Rewired `interpreter/eval/pipeline/command-execution.ts` to delegate command branch execution to `executeCommandHandler`.
- Added focused parity and behavior tests in `interpreter/eval/pipeline/command-execution/handlers/execute-command.test.ts`.

Checklist items completed:
- [x] Extract command/provider execution handler with explicit inputs and outputs.
- [x] Preserve provider/non-provider parity for interpolation, using/auth injection, and dispatch contracts.
- [x] Preserve nested with-clause pipeline chaining behavior.
- [x] Preserve retry propagation and descriptor merge behavior.

Tests run:
- `NODE_ENV=test MLLD_NO_STREAMING=true vitest run interpreter/eval/pipeline/command-execution/handlers/execute-command.test.ts interpreter/eval/pipeline/command-execution.characterization.test.ts interpreter/eval/pipeline/command-execution/structured-input.test.ts interpreter/eval/pipeline/command-execution/normalize-executable.test.ts interpreter/eval/pipeline/command-execution/bind-pipeline-params.test.ts interpreter/eval/pipeline/command-execution/resolve-command-reference.test.ts interpreter/eval/pipeline/command-execution/preflight/guard-preflight.test.ts interpreter/eval/pipeline/command-execution/preflight/policy-preflight.test.ts` -> PASS
- `npm run build && npm test && npm run test:tokens && npm run test:examples` -> PASS

**2026-02-10 11:12 UTC:** Phase completion verification:
- Re-ran full gate after commit.
- `npm run build && npm test && npm run test:tokens && npm run test:examples` -> PASS

Ready to close phase.
