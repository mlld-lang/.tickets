---
id: m-ff50
status: open
deps: []
created: 2026-02-09T07:03:52Z
type: epic
priority: 1
assignee: Adam Avenir
tags: [refactor, show-evaluator]
updated: 2026-02-09T07:03:52Z
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

## Acceptance Criteria

1. Phase tickets (0-7) exist under this epic and are dependency-linked in sequence.
2. Each phase ticket includes explicit implementation instructions and explicit exit criteria.
3. Final structure splits responsibilities into cohesive modules under `interpreter/eval/show/` (or equivalent) and reduces `show.ts` to orchestration.
4. Final validation command passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
