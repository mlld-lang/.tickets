---
id: m-9f2b
status: closed
deps: []
links: []
created: 2026-02-09T06:03:26Z
type: epic
priority: 1
assignee: Adam Avenir
tags: [refactor, exec-invocation]
updated: 2026-02-10T20:13:00Z
---
# Refactor Program: Modularize interpreter/eval/exec-invocation.ts

Largest target file: `interpreter/eval/exec-invocation.ts` (~4,090 LOC).

This epic coordinates a phased refactor that decomposes exec invocation behavior into manageable modules while preserving runtime behavior and policy/security semantics.

Execution order:
1. Phase 0 through Phase 9 proceed strictly in order.
2. Each phase closes only after meeting its exit criteria.
3. No phase starts until the previous phase is closed.

Cross-phase constraints:
- Keep `evaluateExecInvocation` as the external entrypoint.
- Preserve behavior for command/code/template/data/pipeline/command-ref/resolver execution paths.
- Preserve guard, policy, streaming, structured value, and security descriptor semantics.
- Keep diffs surgical and verify behavior with characterization tests.

## Wave 2 Architecture Contract

Target end state for `interpreter/eval/exec-invocation.ts`:
- `evaluateExecInvocation` remains the only external entrypoint in this area.
- Argument normalization, builtin/object invocation helpers, guard/policy orchestration, and executable-kind dispatch move behind explicit internal services under `interpreter/eval/exec/`.
- Command and code executable handling live in dedicated runtime handlers, while shared retry/streaming/descriptor behavior stays centralized.
- Shared types and type guards for this area are defined once (for example in `interpreter/eval/exec/types.ts`) and imported by handlers.

Wave 2 guardrails for every phase in this epic:
- Do not create runtime modules under 60 lines unless the file is a pure type-definition file or re-export barrel.
- Do not duplicate pre/post or before/after execution logic; shared behavior stays in one implementation with phase/timing parameters.
- Do not compose services via constructor callback-lambda bundles; use interface-typed service objects.
- Verify dependency direction after each phase; no circular imports in `interpreter/eval/exec*`.

## Minimum Targeted Test Evidence

Every phase note includes targeted coverage from suites that exercise this area, using files such as:
- `interpreter/eval/exec-invocation.structured.test.ts`
- `interpreter/eval/exec-invocation.env-injection.test.ts`
- `interpreter/eval/exec-invocation.autoverify.test.ts`
- `tests/interpreter/hooks/guard-pre-hook.test.ts`

## Acceptance Criteria

1. All phase tickets (0-9) exist as children of this epic.
2. All phase tickets are closed in sequence with evidence attached per ticket.
3. Final architecture splits `exec-invocation.ts` responsibilities into focused modules under `interpreter/eval/exec/` (or equivalent cohesive structure).
4. Final validation command passes:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 20:12 UTC:** Epic completion architecture summary.

Final module layout (`interpreter/eval/exec/` + orchestration):
- `args.ts` (364 LOC): invocation argument evaluation + parameter binding helpers.
- `builtins.ts` (477 LOC): builtin method dispatch, argument normalization, object-source resolution.
- `guard-policy.ts` (684 LOC): guard/policy orchestration, pre/post guard flow, label-flow helpers.
- `streaming.ts` (128 LOC): exec-invocation streaming setup/merge/finalization.
- `context.ts` (84 LOC): invocation context helpers and with-clause auth merge.
- `normalization.ts` (63 LOC): normalization utilities used by exec handlers.
- `security-descriptor.ts` (61 LOC): descriptor extraction/apply helpers for structured carriers.
- `non-command-handlers.ts` (508 LOC): template/data/pipeline/command-ref/section/resolver execution dispatch.
- `command-handler.ts` (795 LOC): command executable execution (autoverify/signature, env/provider injection, fallback shell path, command-level pipeline).
- `code-handler.ts` (406 LOC): code executable execution (mlld-* control-flow languages, language-specific param shaping, shadow env capture, structured normalization).
- `exec-invocation.ts` (1610 LOC): orchestration entrypoint, resolution tracking, builtin/executable routing, post-invocation tail handling.

Callback / cycle-break seams:
- `non-command-handlers.ts` uses `evaluateExecInvocation` service as an explicit recursion seam for command-ref re-entry.
  - Reason: command-ref execution re-invokes the same entrypoint with merged invocation context while avoiding cyclic direct module imports.
- `code-handler.ts` receives `finalizeResult`/descriptor accessors as orchestration services.
  - Reason: preserves single guard/post-processing ownership in the orchestration layer while allowing handler-local early-return control flow.

Module count and approximate distribution:
- 10 extracted runtime modules under `interpreter/eval/exec/` totaling 3,570 LOC.
- Orchestration file (`exec-invocation.ts`) at 1,610 LOC.
- Area total: 5,180 LOC.
- Distribution shape: one orchestration module + three large execution handlers + medium guard/builtin/args modules + small context/normalization/security/streaming helpers.

Verification status:
- Full gate passed on final phase close:
  - `npm run build && npm test && npm run test:tokens && npm run test:examples`
