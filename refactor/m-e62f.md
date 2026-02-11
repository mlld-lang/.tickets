---
id: m-e62f
status: closed
deps: [m-1f33]
created: 2026-02-09T06:44:04Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-bd66
tags: [refactor, run, phase-2]
updated: 2026-02-11T07:05:23Z
---
# Refactor Program: Modularize interpreter/eval/run.ts - Phase 2: Extract operation-context and policy guard utilities

Objective:
Isolate policy and operation-context logic so command, code, and executable paths share a consistent guard pipeline.

Instructions for the implementing agent:
- Extract helpers that build and apply operation context (labels, sources, metadata) for command and capability operations.
- Extract policy check helpers for command access and capability access, preserving existing error types and codes.
- Extract label-flow guard helpers for arg/stdin/using channels and output-policy descriptor derivation.
- Centralize descriptor-to-taint conversion call sites so run branches use shared utilities.
- Ensure helper contracts are explicit and easy to unit test.
- Add tests that verify deny/allow behavior and channel-specific label-flow checks remain unchanged.

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

1. Operation-context and policy/taint checks are extracted into dedicated utilities and reused across branches.
2. Error type/code/message behavior for denied operations remains consistent.
3. New tests cover command/capability denial and label-flow channel behavior.
4. Exit criteria (required before next phase): full test gate passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples


**2026-02-11 07:05 UTC:** Phase 2 extraction completed.

What changed:
- Added shared policy/context utility module `interpreter/eval/run-modules/run-policy-context.ts`.
- Extracted operation-context helpers:
  - `applyRunOperationContext`
  - `buildRunCommandOperationUpdate`
  - `buildRunCapabilityOperationUpdate`
- Extracted policy enforcement helpers:
  - `enforceRunCommandPolicy`
  - `enforceRunCapabilityPolicy`
- Extracted label-flow helpers and centralized descriptor->taint conversion:
  - `checkRunInputLabelFlow`
  - `deriveRunOutputPolicyDescriptor`
- Rewired `interpreter/eval/run.ts` command/code/executable branches to consume these helpers.
- Added focused helper tests in `interpreter/eval/run-modules/run-policy-context.test.ts` for deny/allow behavior and channel-specific label-flow checks.

Touched runtime modules (ownership + line count):
- `interpreter/eval/run.ts` (1440 LOC): orchestrates run subtypes and now delegates policy/context helper logic.
- `interpreter/eval/run-modules/run-policy-context.ts` (164 LOC): operation-context construction/application, policy checks, and label-flow utilities.

Guardrail confirmations:
- No new runtime module under 60 LOC introduced.
- No callback-lambda service injection introduced.
- Shared policy/context/label-flow logic extracted once; no duplication introduced across run branches.
- Module dependencies remain acyclic in the touched run module area.

Sub-checkpoint evidence (pass):
- `npm run build && npx vitest run interpreter/eval/run-modules/run-modules.test.ts interpreter/eval/run-modules/run-policy-context.test.ts interpreter/eval/run.characterization.test.ts interpreter/eval/run.structured.test.ts interpreter/eval/run-in-blocks.test.ts tests/integration/policy-command-exec.test.ts tests/pipeline-context-retry.test.ts`

Full gate evidence (pass):
- `npm run build && npm test && npm run test:tokens && npm run test:examples`
