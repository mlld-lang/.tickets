---
id: m-aa0a
status: open
deps: [m-16bd]
created: 2026-02-09T07:04:59Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-a97c
tags: [refactor, variable-importer, phase-1]
updated: 2026-02-09T07:04:59Z
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
