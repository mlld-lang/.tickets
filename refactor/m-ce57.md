---
id: m-ce57
status: open
deps: []
created: 2026-02-09T07:06:39Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-9747
tags: [refactor, core-interpreter, phase-0]
updated: 2026-02-09T07:06:39Z
---
# Refactor Program: Modularize interpreter/core/interpreter.ts - Phase 0: Baseline and characterization



Objective:
Establish high-fidelity baseline behavior across core interpreter evaluation paths.

Instructions for the implementing agent:
- In scope: behavior characterization and extraction-map preparation only.
- Out of scope: moving logic into new modules, changing dispatch rules, or changing output/effect ordering.
- Map responsibilities across array traversal, single-node dispatch, variable/pipeline resolution, interpolation security recording, and namespace display cleanup.
- Add characterization tests for document arrays vs single-node evaluation, expression-context behavior, node dispatch paths, and effect/intent emission ordering.
- Record extraction seams and invariants for `evaluate`, `interpolate`, and `cleanNamespaceForDisplay`.

Module boundaries for later phases:
- Boundary map defines modules under `interpreter/core/interpreter/` for traversal, dispatch, resolvers, interpolation/security, and namespace utilities.
- Public exports remain stable: `evaluate`, `interpolate`, `cleanNamespaceForDisplay`.

Preserve behavior checks:
- `evaluate` return shape and side effects remain baseline-locked across document and expression contexts.
- Node dispatch semantics and unknown-node errors remain stable.
- Interpolation security descriptor recording remains stable.
- Field/pipeline resolution behavior remains stable.
- Intent/effect ordering and reconstruction behavior remain stable.

## Acceptance Criteria

1. No production code extraction occurs; this phase adds characterization coverage and an extraction map only.
2. Baseline tests assert preserve-behavior checks for API stability, dispatch semantics, resolver behavior, security recording, and effect ordering.
3. Baseline assertions are concrete enough to detect behavioral drift in later phases.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
