---
id: m-b1de
status: closed
deps: [m-e379]
links: []
created: 2026-02-09T06:03:26Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-9f2b
tags: [refactor, exec-invocation, phase-3, builtins]
updated: 2026-02-10T19:16:53Z
---
# Phase 3: Extract builtin and object invocation flow

Objective:
Extract builtin/object invocation flow and reduce duplicated with-clause/pipeline handling.

Instructions for the implementing agent:
- Move builtin method dispatch and object source/reference resolution into `interpreter/eval/exec/builtins.ts`.
- Preserve behavior for string/array/search/type-check builtins and post-field access.
- Introduce shared helper(s) for applying pipeline + with-clause handling to avoid repeated blocks.
- Keep error messages and edge-case behavior consistent.

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

1. Builtin/object invocation logic lives in focused module(s).
2. Duplicated pipeline/with-clause handling in builtin branches is reduced via shared helper(s).
3. Builtin-related tests (including split/indexing/pipeline flows) remain green.
4. Full test gate passes and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples


**2026-02-10 19:16 UTC:** Completed Phase 3 builtin/object invocation extraction and duplicated with-clause reduction.

Checklist completion:
- [x] Moved builtin method dispatch and object source/reference resolution into `interpreter/eval/exec/builtins.ts`.
- [x] Preserved string/array/search/type-check builtin behavior and post-field access handling.
- [x] Introduced shared helper `applyInvocationWithClause` in `exec-invocation.ts` to reduce repeated pipeline + with-clause handling across builtin branches.
- [x] Kept builtin error/edge messages aligned (`Object not found`, `Cannot access field ... on non-object`, `Unable to resolve object value for builtin method invocation`, `Unknown builtin method`).

Touched runtime modules and ownership:
- `interpreter/eval/exec/builtins.ts` (477 LOC): builtin registry/type guards, builtin dispatch implementation, argument evaluation helper, objectReference/objectSource resolution flow.
- `interpreter/eval/exec-invocation.ts` (3488 LOC): delegates builtin/object flow to `exec/builtins.ts` and centralizes invocation with-clause application via shared helper.

Wave 2 guardrail attestation:
- No new runtime module under 60 LOC was introduced.
- No callback-lambda service-composition bundles were introduced.
- One callback seam exists for recursion/cycle-breaking only: `resolveBuiltinInvocationObject` accepts `evaluateExecInvocationNode` to evaluate `objectSource` exec-invocations without introducing module import cycles.
- Shared builtin types/dispatch/guards are defined once in `exec/builtins.ts` and imported by call sites.
- Dependency direction remains acyclic in touched area (`exec-invocation.ts` -> `exec/builtins.ts`; no reverse import).

Targeted test evidence:
- `npx vitest run interpreter/eval/exec-invocation.structured.test.ts interpreter/methods.split.test.ts tests/pipeline/exec-structured.test.ts interpreter/eval/exec-invocation.autoverify.test.ts interpreter/eval/exec-invocation.env-injection.test.ts` => PASS.

Full gate evidence:
- `npm run build && npm test && npm run test:tokens && npm run test:examples` => PASS.
