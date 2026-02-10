---
id: m-e21c
status: open
deps: [m-aa0a]
created: 2026-02-09T07:04:59Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-a97c
tags: [refactor, variable-importer, phase-2]
updated: 2026-02-09T07:04:59Z
---
# Refactor Program: Modularize interpreter/eval/import/VariableImporter.ts - Phase 2: Extract module export serialization engine



Objective:
Modularize module export serialization and manifest enforcement.

Instructions for the implementing agent:
- In scope: `processModuleExports` decomposition into serialization/manifest/guard-export helpers.
- Out of scope: import routing and variable factory extraction.
- Extract `processModuleExports` into dedicated export-serialization services (manifest validation, guard export checks, descriptor merge/metadata serialization).
- Preserve export filtering behavior for system variables and explicit export manifest handling.
- Add tests for explicit export validation, guard export errors, and serialized metadata output.

Module boundaries:
- Serialization module owns descriptor/metadata serialization.
- Manifest validator owns explicit export manifest checks.
- Guard export checker owns guard export restrictions.

Preserve behavior checks:
- Export filtering for system variables remains unchanged.
- Explicit export manifest handling remains unchanged.
- Guard export error semantics remain unchanged.
- Serialized metadata compatibility remains unchanged.

## Acceptance Criteria

1. Module export serialization logic is extracted into dedicated services.
2. Manifest enforcement and metadata serialization remain baseline-compatible.
3. Tests cover manifest validation, guard export restrictions, and serialization parity.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
