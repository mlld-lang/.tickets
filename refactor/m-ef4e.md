---
id: m-ef4e
status: closed
deps: [m-9f2b]
links: []
created: 2026-02-09T06:19:05Z
type: epic
priority: 1
assignee: Adam Avenir
tags: [refactor, environment]
updated: 2026-02-10T21:52:19Z
---
# Refactor Program: Modularize interpreter/env/Environment.ts

Largest unplanned target file: `interpreter/env/Environment.ts` (~3,158 LOC).

This epic coordinates a phased refactor that decomposes `Environment` into focused collaborators while preserving runtime behavior, security semantics, and public APIs consumed across the interpreter.

Execution order:
1. Phase 0 through Phase 8 proceed strictly in order.
2. Each phase closes only after its exit criteria are met.
3. No phase starts until its dependency ticket is closed.

Cross-phase constraints:
- Keep `Environment` public method signatures stable unless a phase explicitly includes an API migration with complete call-site updates.
- Preserve root/child environment semantics, policy/tool scoping, resolver behavior, import guards, and provenance/security descriptor propagation.
- Preserve output/effect ordering, streaming behavior, and SDK event emission.
- Keep diffs surgical and backed by characterization tests before structural changes.

## Wave 2 Architecture Contract

Target end state for `interpreter/env/Environment.ts`:
- `Environment` remains a facade/composition root and keeps public method contracts stable.
- Security/policy/state-write runtime, resolver/variable orchestration, effects/SDK bridge, shadow env/execution wrappers, and child scope lifecycle each have focused collaborators.
- Core invariants stay intact: root/child semantics, resolver cache behavior, guard context integration, and effect/stream ordering.
- Shared internal contracts for env collaborators are defined once and reused.

Wave 2 guardrails for every phase in this epic:
- Do not create runtime modules under 60 lines unless the file is a pure type-definition file or re-export barrel.
- Do not split thin wrappers into many tiny files when a cohesive collaborator module is clearer.
- Do not use callback-lambda constructor injection for collaborator composition except recursive evaluation seams.
- Verify no circular dependencies across `interpreter/env/*` after each phase.

## Minimum Targeted Test Evidence

Every phase note includes targeted coverage from suites that exercise this area, using files such as:
- `interpreter/env/NodeShadowEnvironment.test.ts`
- `interpreter/env/primitive-variable.test.ts`
- `interpreter/env/variable-proxy-integration.test.ts`
- `interpreter/env/variable-passing-integration.test.ts`
- `interpreter/env/executors/ShellCommandExecutor.test.ts`

## Acceptance Criteria

1. Phase tickets (0-8) exist under this epic and are dependency-linked in sequence.
2. Each phase ticket includes implementation instructions and explicit exit criteria.
3. Final structure splits major responsibilities into cohesive modules under `interpreter/env/` (or a nearby focused subdirectory) and reduces `Environment.ts` to orchestration/composition responsibilities.
4. Final validation command passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 21:52 UTC:** Environment epic completion architecture note

Scope complete
- All phase tickets `m-5cd8 -> m-edc5 -> m-499a -> m-8731 -> m-cb49 -> m-8006 -> m-408b -> m-bfa9 -> m-b140` are closed.

Resulting module set and responsibilities
- `interpreter/env/Environment.ts` (2129 lines): composition root/facade; preserves public API surface and delegates focused responsibilities.
- `interpreter/env/bootstrap/EnvironmentBootstrap.ts` (289 lines): bootstrap/dependency wiring and root environment initialization.
- `interpreter/env/runtime/ExecutionOrchestrator.ts` (193 lines): command/code execution orchestration boundary.
- `interpreter/env/runtime/ShadowEnvironmentRuntime.ts` (195 lines): JS/Node shadow environment lifecycle and wrappers.
- `interpreter/env/runtime/OutputCoordinator.ts` (142 lines): output/effect routing and ordering helpers.
- `interpreter/env/runtime/SdkEventBridge.ts` (104 lines): SDK event emission bridge.
- `interpreter/env/runtime/SecurityPolicyRuntime.ts` (260 lines): policy state, descriptors, trust labels, and guard/security coordination.
- `interpreter/env/runtime/StateWriteRuntime.ts` (115 lines): `@state` write-through behavior and dynamic-module state interactions.
- `interpreter/env/runtime/ContextFacade.ts` (126 lines): operation/pipeline/guard context access and updates.
- `interpreter/env/runtime/ResolverVariableFacade.ts` (198 lines): resolver-variable hydration and import binding bookkeeping.
- `interpreter/env/runtime/VariableFacade.ts` (93 lines): shared variable helper operations.
- `interpreter/env/runtime/ChildEnvironmentLifecycle.ts` (107 lines): child creation/merge lifecycle semantics.
- `interpreter/env/runtime/RuntimeConfigurationRuntime.ts` (284 lines): runtime toggles/configuration (URL/path/streaming/provenance/local module behavior).
- `interpreter/env/runtime/DiagnosticsRuntime.ts` (186 lines): collected-error display, directive tracing, source-cache helpers.

Callback / cycle-break seam accounting
- No callback-lambda constructor injection was introduced for collaborator composition in this epic.
- No cycle-break callback seam is required in the extracted Environment runtime area; dependency direction remains acyclic with `Environment` as composition root.

Module count and approximate line distribution (core extracted area)
- Total modules in core Environment composition area: 14 (`Environment` + `EnvironmentBootstrap` + 12 runtime collaborators).
- Approximate LOC distribution:
  - Facade: `Environment.ts` ~2129 LOC
  - Bootstrap: `EnvironmentBootstrap.ts` ~289 LOC
  - Runtime collaborators (12 files): ~2003 LOC total
  - Combined core area total: ~4421 LOC

Epic boundary validation
- Full gate command executed and passed:
  - `npm run build && npm test && npm run test:tokens && npm run test:examples`
- Final pass status observed:
  - build: PASS
  - test: PASS
  - test:tokens: PASS
  - test:examples: PASS
