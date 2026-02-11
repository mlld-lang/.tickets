---
id: m-f79f
status: closed
deps: [m-5bf5]
created: 2026-02-09T07:06:40Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-9747
tags: [refactor, core-interpreter, phase-7]
updated: 2026-02-10T12:50:20Z
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

**2026-02-10 12:48 UTC:** Phase implementation complete:
- Reduced interpreter/core/interpreter.ts to orchestration-only composition for public APIs:
  - evaluate(node|nodes, env, context?)
  - interpolate(...)
  - cleanNamespaceForDisplay(...)
- Extracted core dispatch/evaluation flow into interpreter/core/interpreter/evaluator.ts.
- Kept extracted handler module boundaries and wired evaluate through evaluateCore() with explicit dependencies.
- Added architecture ownership notes in docs/dev/INTERPRETER.md (Entry Points section) describing interpreter.ts entrypoint composition and evaluator.ts ownership.
- Verified internal dependency graph for interpreter/core/interpreter/*.ts is acyclic.

Acceptance checklist status:
- [x] interpreter.ts is orchestration-focused
- [x] public API contracts remain stable
- [x] parity checks cover dispatch/resolution/interpolation/effect ordering behavior

Checks run:
- npx vitest run interpreter/core/interpreter.characterization.test.ts interpreter/core/interpreter/dispatch.test.ts interpreter/core/interpreter/traversal.test.ts interpreter/core/interpreter/resolve-variable-reference.test.ts interpreter/core/interpreter/interpolation-security.test.ts interpreter/core/interpreter/namespace-display.test.ts interpreter/core/interpreter/value-resolution.test.ts interpreter/core/interpreter/handlers/specialized-handlers.test.ts => PASS
- node internal cycle check script for interpreter/core/interpreter/*.ts => PASS (No internal cycles)
- npm run build && npm test && npm run test:tokens && npm run test:examples => PASS

**2026-02-10 12:50 UTC:** Post-commit verification complete after commit 582a83666. Full gate rerun passed: npm run build && npm test && npm run test:tokens && npm run test:examples. Phase exit criteria satisfied; closing phase.
