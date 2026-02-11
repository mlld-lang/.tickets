---
id: m-bfa9
status: closed
deps: [m-408b]
links: []
created: 2026-02-09T06:19:05Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-ef4e
tags: [refactor, environment, phase-7, config, diagnostics]
updated: 2026-02-10T21:41:13Z
---
# Refactor Program: Modularize interpreter/env/Environment.ts - Phase 7: Extract runtime configuration and diagnostics services

Objective:
Separate runtime configuration toggles and diagnostics paths from core environment orchestration.

Instructions for the implementing agent:
- Extract runtime-configuration methods into dedicated services/modules:
  - URL/path/streaming/provenance toggles
  - local module configuration
  - ephemeral mode reconfiguration flow
- Extract diagnostics/support utilities:
  - collected error formatting/display
  - directive trace stack handling and timing
  - source cache access
- Preserve externally observable behavior and debug/provenance event emission.

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

1. Runtime configuration and diagnostics concerns are moved to focused modules.
2. Ephemeral mode and local module setup behavior remain unchanged.
3. Trace/debug/error-display behavior remains unchanged in tests.
4. Exit criteria: full test gate passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples


**2026-02-10 21:41 UTC:** Phase 7 implementation complete.

Changes made
- Added `interpreter/env/runtime/RuntimeConfigurationRuntime.ts` (280 LOC): owns runtime-configuration concerns for URL/path/streaming/provenance toggles, dynamic-mode/blank-line/fuzzy toggles, local-module configuration, and ephemeral resolver/registry reconfiguration helpers.
- Added `interpreter/env/runtime/DiagnosticsRuntime.ts` (219 LOC): owns diagnostics concerns for collected-error collection/display, directive trace stack/timing lifecycle, and source-cache read/write behavior.
- Updated `interpreter/env/Environment.ts` (2171 LOC total; phase diff includes major method delegation):
  - Runtime config methods now delegate to `RuntimeConfigurationRuntime`.
  - Ephemeral mode and local-module configuration delegate to runtime config service.
  - Error collection/display, trace push/pop/mark/toggle, and source cache methods delegate to `DiagnosticsRuntime`.
- Added focused tests:
  - `interpreter/env/runtime/RuntimeConfigurationRuntime.test.ts` (115 LOC)
  - `interpreter/env/runtime/DiagnosticsRuntime.test.ts` (110 LOC)

Checklist status
- [x] Runtime configuration concerns moved to focused module(s).
- [x] Ephemeral mode and local module setup behavior preserved.
- [x] Trace/debug/error-display behavior preserved.

Validation
- Sub-phase targeted validation (runtime-config extraction checkpoint):
  - `npm run build && npm test -- --run interpreter/env/Environment.characterization.test.ts interpreter/stream-execution.test.ts interpreter/debug-dynamic-import.test.ts tests/ephemeral-mode.test.ts`
  - Result: PASS
- Sub-phase targeted validation (diagnostics extraction checkpoint + new runtime tests):
  - `npm run build && npm test -- --run interpreter/env/runtime/RuntimeConfigurationRuntime.test.ts interpreter/env/runtime/DiagnosticsRuntime.test.ts interpreter/env/Environment.characterization.test.ts core/errors/enhanced-error-display.test.ts interpreter/stream-execution.test.ts interpreter/debug-dynamic-import.test.ts tests/ephemeral-mode.test.ts`
  - Result: PASS
- Phase close full gate:
  - `npm run build && npm test && npm run test:tokens && npm run test:examples`
  - Result: PASS

Wave 2 guardrail evidence
- No new runtime module under 60 LOC introduced. New runtime modules are 280 LOC and 219 LOC.
- No callback-lambda service-composition injection introduced. Environment composes service objects (`RuntimeConfigurationRuntime`, `DiagnosticsRuntime`) and delegates behavior.
- Shared runtime config/diagnostics logic is centralized in dedicated modules; no duplicated sibling implementations were introduced.
- Touched dependency direction remains acyclic (Environment facade -> runtime services; services depend on shared/core/env abstractions only and do not import Environment).
