---
id: m-ff50
status: closed
deps: [m-04d8]
created: 2026-02-09T07:03:52Z
type: epic
priority: 1
assignee: Adam Avenir
tags: [refactor, show-evaluator]
updated: 2026-02-11T18:35:17Z
---
# Refactor Program: Modularize interpreter/eval/show.ts



Largest unplanned target file: `interpreter/eval/show.ts` (~1,430 LOC).

This epic coordinates a phased refactor that decomposes show/add evaluation into focused handlers while preserving subtype behavior, variable/display formatting semantics, policy checks, and effect emission contracts.

Execution order:
1. Phase 0 through Phase 7 proceed strictly in order.
2. Each phase closes only after its exit criteria are met.
3. No phase starts until its dependency ticket is closed.

Cross-phase constraints:
- Keep exported API `evaluateShow` stable and preserve supported subtypes.
- Preserve variable-resolution semantics (including field access, pipeline tails, and structured-value display behavior).
- Preserve invocation handling semantics for executable and method-style calls.
- Preserve policy label-flow enforcement and security descriptor propagation.
- Preserve output/effect emission behavior including newline normalization and wrapper return shape.

## Wave 2 Architecture Contract

Target end state for `interpreter/eval/show.ts`:
- `evaluateShow` remains the external entrypoint and becomes a thin dispatcher.
- Variable resolution pipeline, path/template/load-content handlers, invocation handlers, foreach handlers, command/code handlers, and output transforms are isolated under `interpreter/eval/show/`.
- Field access, pipeline tails, method-call semantics, and effect emission behavior remain unchanged.
- Shared show handler contracts/types are defined once and reused across subtype handlers.

Wave 2 guardrails for every phase in this epic:
- Do not create runtime modules under 60 lines unless the file is a pure type-definition file or re-export barrel.
- Do not duplicate conversion/display helpers across handlers.
- Do not compose handlers via callback-lambda constructor bundles.
- Verify no circular dependencies inside `interpreter/eval/show*` after each phase.

## Minimum Targeted Test Evidence

Every phase note includes targeted coverage from suites that exercise this area, using files such as:
- `interpreter/eval/show.structured.test.ts`
- `interpreter/eval/alligator.structured.test.ts`
- `tests/interpreter/security-metadata.test.ts`
- representative token fixtures under `tests/tokens/alligator/*`

## Acceptance Criteria

1. Phase tickets (0-7) exist under this epic and are dependency-linked in sequence.
2. Each phase ticket includes explicit implementation instructions and explicit exit criteria.
3. Final structure splits responsibilities into cohesive modules under `interpreter/eval/show/` (or equivalent) and reduces `show.ts` to orchestration.
4. Final validation command passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-11 18:35 UTC:** --dir refactor
