---
id: m-f79f
status: open
deps: [m-5bf5]
created: 2026-02-09T07:06:40Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-9747
tags: [refactor, core-interpreter, phase-7]
updated: 2026-02-09T07:06:41Z
---
# Refactor Program: Modularize interpreter/core/interpreter.ts - Phase 7: Final composition cleanup and contract verification



Objective:
Reduce `interpreter.ts` to orchestration and lock contracts for core interpreter APIs.

Instructions for the implementing agent:
- In scope: final orchestration cleanup, dependency graph cleanup, and contract verification.
- Out of scope: introducing new runtime semantics or changing public interpreter behavior.
- Refactor top-level exports to compose extracted traversal, dispatch, resolver, interpolation/security, and utility modules with minimal inline logic.
- Remove duplicate helper paths and confirm acyclic dependencies across `interpreter/core/interpreter/*`.
- Attach architecture notes describing entrypoint flow and module ownership.
- Run final parity checks against Phase 0 baselines for `evaluate`, `interpolate`, and `cleanNamespaceForDisplay`.

Module boundaries:
- `interpreter.ts` acts as orchestration-only entrypoint.
- Extracted modules have one-directional dependencies into shared utility layers.
- Public APIs and return-shape contracts remain unchanged.

Preserve behavior checks:
- `evaluate`, `interpolate`, and `cleanNamespaceForDisplay` signatures and behavior remain stable.
- Node dispatch semantics and unknown-node handling remain stable.
- Field/pipeline resolution remains stable.
- Interpolation security recording remains stable.
- Intent/effect ordering remains stable.

## Acceptance Criteria

1. `interpreter.ts` is orchestration-focused with clear, acyclic module dependencies.
2. Public APIs and runtime behavior remain baseline-compatible.
3. Final parity checks cover preserve-behavior requirements across dispatch, resolution, interpolation, and effect ordering.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
