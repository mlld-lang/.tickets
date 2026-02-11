---
id: m-cb49
status: closed
deps: [m-8731]
links: []
created: 2026-02-09T06:19:05Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-ef4e
tags: [refactor, environment, phase-4, effects, context, sdk]
updated: 2026-02-10T21:08:35Z
---
# Refactor Program: Modularize interpreter/env/Environment.ts - Phase 4: Extract effects, context wrappers, and SDK bridge

Objective:
Isolate output/effect routing and context-wrapper behavior from core environment state.

Instructions for the implementing agent:
- Extract effect and intent routing logic (`emitEffect`, `intentToEffect`, `emitIntent`, `renderOutput`) into a dedicated output coordinator.
- Extract SDK stream/command bridge mapping and event emission helpers.
- Consolidate thin wrappers around `ContextManager` and `GuardRegistry` into a coherent context facade.
- Preserve ordering behavior, import-time doc suppression, break collapsing behavior, and provenance inclusion in SDK events.

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

1. Effect/intention handling and SDK bridge logic are isolated into focused components.
2. Context wrapper behavior (pipeline/guard/operation/tool-call tracking) remains unchanged.
3. Streaming/event and effect-ordering tests remain green.
4. Exit criteria: full test gate passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples


**2026-02-10 21:08 UTC:** Phase 4 complete: extracted effects/context/SDK bridge from Environment runtime.

Checklist coverage:
- Extracted effect and intent routing into OutputCoordinator and wired Environment emitEffect/intent/output methods through it.
- Extracted SDK stream/command bridge into SdkEventBridge and migrated enable/emit/cleanup paths.
- Extracted ContextManager + GuardRegistry wrapper surface into ContextFacade and migrated pipeline/guard/op/tool/generic context wrappers.
- Preserved import-time doc suppression, break-collapsing flush behavior, and SDK provenance payload emission behavior.

Touched runtime modules (line counts + ownership):
- interpreter/env/runtime/OutputCoordinator.ts (142): owns output intent->effect routing, capability/provenance enrichment, and effect handler dispatch.
- interpreter/env/runtime/SdkEventBridge.ts (104): owns StreamBus subscription lifecycle and StreamEvent->SDK event mapping.
- interpreter/env/runtime/ContextFacade.ts (126): owns Environment context-wrapper delegation and shared pipeline guard history registry interactions.

Additional tests added:
- interpreter/env/runtime/OutputCoordinator.test.ts
- interpreter/env/runtime/SdkEventBridge.test.ts
- interpreter/env/runtime/ContextFacade.test.ts
- Updated interpreter/env/Environment.characterization.test.ts cleanup assertions for bridge extraction.

Validation executed:
- Sub-checkpoint build: `npm run build` ✅
- Sub-checkpoint targeted behavior: `npm test -- --run interpreter/env/runtime/ContextFacade.test.ts interpreter/env/runtime/SdkEventBridge.test.ts interpreter/env/runtime/OutputCoordinator.test.ts interpreter/env/Environment.characterization.test.ts interpreter/stream-execution.test.ts interpreter/debug-dynamic-import.test.ts tests/interpreter/hooks/guard-pre-hook.test.ts` ✅
- Phase full gate: `npm run build && npm test && npm run test:tokens && npm run test:examples` ✅

Wave 2 quality guardrails:
- No new sub-60 runtime modules introduced in touched extraction area.
- No duplicated pre/post runtime logic introduced across extracted siblings.
- No callback-lambda service-composition injection introduced; one OutputRenderer callback seam remains as the renderer callback entrypoint for local intent->effect routing.
- Shared effect/context contracts are centralized in runtime module exports and reused by Environment.

Dependency direction in touched area remains acyclic.
