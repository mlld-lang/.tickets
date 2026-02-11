---
id: m-8aeb
status: closed
deps: [m-86a1]
created: 2026-02-09T07:05:00Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-a97c
tags: [refactor, variable-importer, phase-7]
updated: 2026-02-10T15:02:50Z
---
# Refactor Program: Modularize interpreter/eval/import/VariableImporter.ts - Phase 7: Final composition cleanup and serialization boundary verification



Objective:
Reduce `VariableImporter.ts` to orchestration with stable contracts.

Instructions for the implementing agent:
- In scope: final orchestration cleanup, dependency-direction verification, and parity checks.
- Out of scope: introducing new import/export semantics.
- Refactor top-level methods to delegate to extracted services with minimal inline branching.
- Remove duplicate utility code and verify dependency flow remains acyclic.
- Attach architecture notes describing serialization and import-routing boundaries.
- Run final parity checks across export serialization, import routing, factory strategy routing, executable rehydration, and namespace/policy import behavior.

Module boundaries:
- `VariableImporter.ts` is orchestration-only after this phase.
- Extracted modules have acyclic dependencies and explicit ownership (binding, serialization, routing, factory strategies, executable recovery, namespace/policy handlers).
- Public APIs remain stable: `importVariables`, `processModuleExports`, `createVariableFromValue`.

Preserve behavior checks:
- Serialization/deserialization compatibility remains unchanged.
- Module export/import path behavior remains unchanged.
- Variable factory routing behavior remains unchanged.
- Executable rehydration and captured env recovery remain unchanged.
- Namespace/policy import behavior remains unchanged.

Micro-checklist (final serialization parity gate):
- Add end-to-end roundtrip tests for exported executables with nested captured env stacks.
- Add mixed import sets (namespace + selected + policy imports) and assert unchanged binding results and guard registration behavior.
- Add serialization-compatibility assertions for metadata and descriptors across roundtrip import/export cycles.
- Add collision and policy-needs edge cases in the same import batch and assert unchanged precedence.

## Acceptance Criteria

1. `VariableImporter.ts` is orchestration-focused with clear, acyclic module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain baseline-compatible.
3. Final parity checks cover all preserve-behavior requirements listed in this ticket.
4. Every item in the micro-checklist is backed by explicit tests before closing this phase.
5. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 15:02 UTC:** --dir refactor
