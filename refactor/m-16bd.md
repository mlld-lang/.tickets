---
id: m-16bd
status: closed
deps: []
created: 2026-02-09T07:04:59Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-a97c
tags: [refactor, variable-importer, phase-0]
updated: 2026-02-10T14:07:44Z
---
# Refactor Program: Modularize interpreter/eval/import/VariableImporter.ts - Phase 0: Baseline and characterization



Objective:
Establish baseline behavior across import/export variable flows.

Instructions for the implementing agent:
- In scope: characterization coverage and extraction-boundary mapping only.
- Out of scope: moving logic across files or changing behavior.
- Map responsibilities across export serialization, import type routing, variable reconstruction, executable rehydration, and policy/guard registration.
- Add characterization coverage for selected vs namespace imports, metadata propagation, executable import behavior, and collision paths.
- Capture extraction seams and regression risks; do not move code across files in this phase.

Preserve behavior checks:
- Serialization/deserialization compatibility remains baseline-locked.
- Module export/import path behavior remains baseline-locked.
- Variable factory routing behavior remains baseline-locked.
- Executable rehydration and captured env recovery remain baseline-locked.
- Namespace/policy import behavior remains baseline-locked.

## Acceptance Criteria

1. Characterization coverage exists for all listed preserve-behavior checks and high-risk branches.
2. Baseline notes include explicit extraction boundaries and regression hazards.
3. No intentional runtime semantic change is introduced.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 14:06 UTC:** --dir refactor Implemented Phase 0 baseline characterization (no production code movement):
- Added interpreter/eval/import/variable-importer.characterization.test.ts with coverage for:
  - selected vs namespace import flows,
  - metadata propagation,
  - executable export serialization + rehydration + captured env recovery,
  - import binding collision paths,
  - variable factory routing behavior,
  - policy namespace import behavior and policy-config recording.
- Added docs/dev/VARIABLE-IMPORTER-EXTRACTION-MAP.md with explicit responsibility map, extraction seams, and regression hazards.
Preserve-behavior checks covered:
- [x] serialization/deserialization compatibility
- [x] module export/import path behavior
- [x] variable factory routing behavior
- [x] executable rehydration and captured env recovery
- [x] namespace/policy import behavior
Tests:
- PASS npx vitest run interpreter/eval/import/*.test.ts
- PASS npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 14:07 UTC:** --dir refactor Post-commit validation complete.
Commit: a6a1c42cb (refactor(variable-importer): complete m-16bd baseline characterization)
Gate:
- PASS npm run build
- PASS npm test
- PASS npm run test:tokens
- PASS npm run test:examples
Phase exit criteria satisfied.
