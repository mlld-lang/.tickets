---
id: m-5ca6
status: closed
deps: [m-0b66]
created: 2026-02-09T07:04:59Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-a97c
tags: [refactor, variable-importer, phase-5]
updated: 2026-02-10T14:44:06Z
---
# Refactor Program: Modularize interpreter/eval/import/VariableImporter.ts - Phase 5: Extract executable rehydration and captured env recovery



Objective:
Isolate executable import reconstruction internals.

Instructions for the implementing agent:
- In scope: executable import reconstruction and captured env deserialize/rehydrate logic.
- Out of scope: general variable factory routing and namespace/policy handlers.
- Extract executable-specific reconstruction (`createExecutableFromImport`) and captured shadow/module env deserialize/rehydrate logic into dedicated modules.
- Preserve behavior for imported executable scope chain preservation and circular-env guardrails.
- Add tests for executable imports with captured shadow/module environments.

Module boundaries:
- Executable reconstruction module owns executable wrapper creation.
- Captured-env rehydration module owns shadow/module env recovery and guardrails.
- Factory module delegates executable values to these modules.

Preserve behavior checks:
- Executable rehydration behavior remains unchanged.
- Captured shadow/module env recovery compatibility remains unchanged.
- Scope chain preservation and circular-env guardrails remain unchanged.

Micro-checklist (captured-env recovery matrix):
- Add nested captured-env stack cases (captured env containing captured env) and assert restored resolution order parity.
- Add mixed shadow-env + module-env recovery cases and assert unchanged scope-chain behavior.
- Add circular-reference guardrail cases and assert unchanged failure behavior.
- Add executable import cases with and without captured env and assert identical non-captured baseline behavior.

## Acceptance Criteria

1. Executable reconstruction and captured env recovery are extracted into dedicated modules.
2. Rehydration semantics and guardrails remain baseline-compatible.
3. Tests cover captured-env executable import parity.
4. Every item in the micro-checklist is backed by explicit tests before closing this phase.
5. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 14:44 UTC:** --dir refactor
