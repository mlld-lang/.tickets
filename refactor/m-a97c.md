---
id: m-a97c
status: closed
deps: [m-943f]
created: 2026-02-09T07:04:59Z
type: epic
priority: 1
assignee: Adam Avenir
tags: [refactor, variable-importer]
updated: 2026-02-10T15:03:16Z
---
# Refactor Program: Modularize interpreter/eval/import/VariableImporter.ts



Largest unplanned target file: `interpreter/eval/import/VariableImporter.ts` (~1,190 LOC).

This epic coordinates a phased refactor that decomposes variable import/export machinery into focused modules while preserving metadata/security serialization, import binding collision behavior, executable rehydration semantics, and namespace/policy import behavior.

Execution order:
1. Phase 0 through Phase 7 proceed strictly in order.
2. Each phase closes only after its exit criteria are met.
3. No phase starts until its dependency ticket is closed.

Cross-phase constraints:
- Keep public API behavior stable: `importVariables`, `processModuleExports`, and `createVariableFromValue`.
- Preserve serialization/deserialization compatibility for captured shadow/module environments.
- Preserve import binding collision handling and detailed import error payloads.
- Preserve executable/template/object/array import reconstruction behavior.
- Preserve policy import behavior (config recording + generated guard registration).

## Acceptance Criteria

1. Phase tickets (0-7) exist under this epic and are dependency-linked in sequence.
2. Each phase ticket includes explicit implementation instructions and explicit exit criteria.
3. Final structure splits responsibilities into cohesive modules under `interpreter/eval/import/variable-importer/` (or equivalent) and reduces `VariableImporter.ts` to orchestration.
4. Final validation command passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-09 08:18 UTC:** Safety-hardening pass completed for phases m-16bd -> m-8aeb.

Tightening applied:
- Added explicit in-scope/out-of-scope boundaries across all phases.
- Added concrete module-boundary requirements for binding guards, serialization, routing, factory strategies, executable rehydration, and namespace/policy handlers.
- Added explicit preserve-behavior checks for serialization/deserialization compatibility, module export/import path behavior, variable factory routing, executable rehydration/captured env recovery, and namespace/policy import behavior.
- Standardized each phase exit criteria to require:
  npm run build && npm test && npm run test:tokens && npm run test:examples

Dependency verification:
- Verified linear chain with `tk dep tree --full m-8aeb` in a temporary workspace mount that maps `.tickets/refactor` as `.tickets`.
- No dependency rewiring required.

Residual risk:
- Captured env recovery remains highest risk because scope-chain restoration intersects with serialization compatibility; parity tests should include nested captured env stacks.

**2026-02-09 08:25 UTC:** Micro-planning addendum (hotspot): added captured-env recovery checklists to phases m-5ca6 and m-8aeb.

Focus:
- Covers nested captured-env stacks and roundtrip serialization compatibility.
- Adds explicit closure gates requiring checklist-backed tests before each phase closes.

**2026-02-10 15:03 UTC:** --dir refactor
