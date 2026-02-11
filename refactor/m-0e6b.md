---
id: m-0e6b
status: closed
deps: [m-98a8]
links: []
created: 2026-02-09T06:03:26Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-9f2b
tags: [refactor, exec-invocation, phase-5, guards, policy]
updated: 2026-02-10T19:40:57Z
---
# Phase 5: Extract guard and policy orchestration

Objective:
Extract guard and policy orchestration from core execution flow.

Instructions for the implementing agent:
- Move operation-context creation, policy capability checks, label-flow enforcement, and guard hook orchestration into `interpreter/eval/exec/guard-policy.ts` (or equivalent).
- Preserve interactions among pre-hook transforms, post-hook handling, retry signaling, and when-expression guard denial handling.
- Keep error types/messages and source-location behavior stable.

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

1. Guard/policy orchestration is isolated in focused module(s).
2. Guard retry and policy denial paths preserve existing behavior.
3. Guard and policy test suites remain green.
4. Full test gate passes and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples


**2026-02-10 19:40 UTC:** Completed Phase 5 extraction for guard and policy orchestration.

Changes:
- interpreter/eval/exec/guard-policy.ts (684 LOC): owns exec guard/policy orchestration primitives:
  - guard input preparation and MCP descriptor merge,
  - operation-context construction + capability and label-flow prechecks,
  - pre-hook transform application,
  - pre-decision handling with when-denial fallback,
  - param label-flow enforcement with when fallback,
  - post-hook execution with guard-denial/warning handling,
  - shared guard arg/variable cloning helpers.
- interpreter/eval/exec-invocation.ts (2841 LOC): delegates guard/policy logic to guard-policy module while preserving execution branches and existing public behavior.

Checklist/acceptance coverage:
- Operation-context creation and policy capability checks extracted.
- Label-flow enforcement paths (including param and output policy handling) extracted and preserved.
- Guard pre/post orchestration, retry/deny handling, and when-expression denial fallback preserved via extracted helpers.

Targeted tests run (PASS):
- npm run build
- npx vitest run interpreter/eval/exec-invocation.structured.test.ts interpreter/eval/exec-invocation.env-injection.test.ts interpreter/eval/exec-invocation.autoverify.test.ts tests/interpreter/hooks/guard-pre-hook.test.ts tests/interpreter/hooks/guard-post-hook.test.ts tests/interpreter/hooks/guard-post-decision-engine.test.ts tests/interpreter/hooks/guard-retry-state.test.ts tests/interpreter/guard-denied-handlers.test.ts tests/integration/policy-command-exec.test.ts tests/integration/policy-label-flow.test.ts
- npx vitest run interpreter/interpreter.fixture.test.ts -t "exceptions/security/no-secret-exfil-run-interpolation"

Full gate run (PASS):
- npm run build && npm test && npm run test:tokens && npm run test:examples

Wave 2 guardrails:
- No new runtime module under 60 LOC introduced in touched area.
- No duplicated guard/policy helper implementation introduced across sibling modules; shared security-descriptor helper is imported from exec/security-descriptor.
- No constructor callback-lambda service injection introduced; extraction uses typed service/options objects.
- Shared extraction-area types/contracts for guard-policy orchestration are centralized in exec/guard-policy.ts and reused by call sites.
- Dependency direction remains acyclic in touched area.
