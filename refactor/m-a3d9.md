---
id: m-a3d9
status: closed
deps: [m-4a76]
created: 2026-02-09T06:44:05Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-bd66
tags: [refactor, run, phase-8]
updated: 2026-02-11T07:45:26Z
---
# Refactor Program: Modularize interpreter/eval/run.ts - Phase 8: Final composition cleanup and architecture verification

Objective:
Finalize modular composition so evaluateRun acts as a thin orchestrator with stable seams and clear ownership.

Instructions for the implementing agent:
- Simplify evaluateRun to orchestration-only flow and remove dead/duplicated logic.
- Validate module boundaries and naming consistency for extracted run modules.
- Ensure imports remain path-alias compliant and types remain strict.
- Add/update architecture notes in docs/dev as needed to reflect current run evaluator structure.
- Run a final pass for readability and maintainability without semantic drift.

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

1. evaluateRun is reduced to orchestration with extracted modules owning branch-specific behavior.
2. No dead code remains from extraction.
3. Architecture notes describe current module boundaries.
4. Exit criteria (required before next phase): full test gate passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples


**2026-02-11 07:45 UTC:** Phase 8 completion evidence (final composition cleanup + architecture verification)

Touched runtime modules (ownership + line counts):
- interpreter/eval/run.ts (355 LOC): evaluateRun orchestration entrypoint; removed inline runExec argument extraction and dead try/catch/finally wrapper.
- interpreter/eval/run-modules/run-exec-definition-dispatcher.ts (699 LOC): executable-definition dispatch owner; added extractRunExecArguments() to own runExec argument/value+descriptor extraction.

Non-runtime doc update:
- docs/dev/INTERPRETER.md: Quick Map /run,/sh entry points to run.ts + run-modules/* boundaries.

Targeted phase tests:
- npx vitest run interpreter/eval/run-modules/run-modules.test.ts interpreter/eval/run-modules/run-policy-context.test.ts interpreter/eval/run-modules/run-exec-resolver.test.ts interpreter/eval/run-modules/run-exec-definition-dispatcher.test.ts interpreter/eval/run-modules/run-output-lifecycle.test.ts interpreter/eval/run.characterization.test.ts interpreter/eval/run.structured.test.ts
- Result: Test Files 7 passed; Tests 64 passed | 1 skipped.

Full gate (required):
- npm run build && npm test && npm run test:tokens && npm run test:examples
- Result: command chain exited 0; build completed successfully; test:examples summary reported Test Files 1 passed, Tests 1353 passed | 2 skipped.

Architecture/dependency verification:
- Local run-evaluator graph check (run.ts + non-test run-modules/*.ts) reports: No cycles in run evaluator local graph.
- Edge map confirms one-way orchestration from run.ts to specialized modules, with leaf helpers remaining acyclic.

Wave-2 guardrail confirmations:
- No new sub-60 runtime module introduced (current non-test run-modules runtime files are >= 74 LOC).
- No callback-lambda service injection introduced.
- No duplicated branch logic added; runExec argument extraction is centralized in dispatcher module.

