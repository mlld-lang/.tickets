---
id: m-abb7
status: open
deps: [m-9edc]
created: 2026-02-09T07:02:43Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-845d
tags: [refactor, pipeline-command-execution, phase-2]
updated: 2026-02-09T07:02:43Z
---
# Refactor Program: Modularize interpreter/eval/pipeline/command-execution.ts - Phase 2: Extract command reference resolution services



Objective:
Modularize command reference lookup and field traversal without changing reference semantics.

Instructions for the implementing agent:
- In scope: extract `resolveCommandReference` internals (variable lookup, dotted-name variant fallback, field traversal).
- Out of scope: execution handlers, parameter binding, guard/policy preflight, or output formatting changes.
- Move reference-resolution internals to `interpreter/eval/pipeline/command-execution/resolve-command-reference.ts` (or equivalent) while keeping exported API stable.
- Keep dotted identifier compatibility behavior and variant fallback ordering unchanged.
- Add tests for missing variant errors, invalid field access, nested path traversal, and transformer variant lookup.

Module boundaries:
- Resolution module accepts explicit inputs (environment, identifiers, path tokens) and returns resolved executable/reference metadata.
- Module does not execute commands or mutate policy/guard state.

Preserve behavior checks:
- `resolveCommandReference` export name and call contract remain unchanged.
- Missing variant and invalid field access errors keep message category and throw point.
- Dotted-name fallback order remains baseline-compatible.
- Command/reference parity holds for equivalent identifiers across direct and dotted forms.

## Acceptance Criteria

1. Resolution internals are extracted into a dedicated module and the public resolver API remains stable.
2. Error semantics and traversal behavior match characterization baselines.
3. Preserve-behavior checks are covered with focused tests.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
