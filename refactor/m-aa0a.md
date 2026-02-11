---
id: m-aa0a
status: closed
deps: [m-16bd]
created: 2026-02-09T07:04:59Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-a97c
tags: [refactor, variable-importer, phase-1]
updated: 2026-02-10T14:15:32Z
---
# Refactor Program: Modularize interpreter/eval/import/VariableImporter.ts - Phase 1: Extract binding guard and metadata parsing helpers



Objective:
Isolate import binding guards and metadata map handling.

Instructions for the implementing agent:
- In scope: binding guard helpers and metadata map parsing extraction only.
- Out of scope: export serialization and variable factory strategy extraction.
- Extract binding helper methods (`ensureImportBindingAvailable`, `setVariableWithImportBinding`) and metadata map parsing utilities.
- Preserve current collision error payloads and binding write semantics.
- Add tests for collision handling and metadata map extraction behavior.

Module boundaries:
- Binding guard module owns collision checks and binding writes.
- Metadata parsing module owns import metadata map extraction.
- Import routing/factory modules consume these utilities through stable interfaces.

Preserve behavior checks:
- Binding collision enforcement remains unchanged.
- Collision error payload shape remains unchanged.
- Metadata map extraction behavior remains unchanged.

## Acceptance Criteria

1. Binding guard and metadata parsing helpers are extracted into focused modules.
2. Collision behavior and binding semantics remain baseline-compatible.
3. Tests cover collision errors and metadata parsing parity.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 14:12 UTC:** --dir refactor Implemented Phase 1 extraction:
- Added interpreter/eval/import/variable-importer/ImportBindingGuards.ts for binding collision checks and binding writes.
- Added interpreter/eval/import/variable-importer/MetadataMapParser.ts for import metadata map extraction.
- Updated VariableImporter to consume extracted helpers without changing import routing/factory behavior.
- Added tests:
  - interpreter/eval/import/variable-importer/import-binding-guards.test.ts
  - interpreter/eval/import/variable-importer/metadata-map-parser.test.ts
Preserve-behavior checks:
- [x] binding collision enforcement unchanged
- [x] collision error payload shape unchanged
- [x] metadata map extraction behavior unchanged
Tests:
- PASS npx vitest run interpreter/eval/import/variable-importer/*.test.ts interpreter/eval/import/variable-importer.characterization.test.ts interpreter/eval/import/*.test.ts
- PASS npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 14:15 UTC:** --dir refactor Post-commit verification and closure evidence:\n- Changes: extracted ImportBindingGuards and MetadataMapParser helpers; rewired VariableImporter to delegate binding collision checks, variable/binding writes, and metadata map parsing.\n- Checklist: completed all phase 1 checklist items for helper extraction and delegation with behavior parity preserved.\n- Targeted tests: npx vitest run interpreter/eval/import/variable-importer/*.test.ts interpreter/eval/import/variable-importer.characterization.test.ts interpreter/eval/import/*.test.ts (pass).\n- Full gate (pass): npm run build && npm test && npm run test:tokens && npm run test:examples.\n- Commit: cd33a7598 (refactor(variable-importer): complete m-aa0a extract binding guards metadata parser).
