---
id: m-ef4e
status: open
deps: []
links: []
created: 2026-02-09T06:19:05Z
type: epic
priority: 1
assignee: Adam Avenir
tags: [refactor, environment]
---
# Refactor Program: Modularize interpreter/env/Environment.ts

Largest unplanned target file: `interpreter/env/Environment.ts` (~3,158 LOC).

This epic coordinates a phased refactor that decomposes `Environment` into focused collaborators while preserving runtime behavior, security semantics, and public APIs consumed across the interpreter.

Execution order:
1. Phase 0 through Phase 8 proceed strictly in order.
2. Each phase closes only after its exit criteria are met.
3. No phase starts until its dependency ticket is closed.

Cross-phase constraints:
- Keep `Environment` public method signatures stable unless a phase explicitly includes an API migration with complete call-site updates.
- Preserve root/child environment semantics, policy/tool scoping, resolver behavior, import guards, and provenance/security descriptor propagation.
- Preserve output/effect ordering, streaming behavior, and SDK event emission.
- Keep diffs surgical and backed by characterization tests before structural changes.

## Acceptance Criteria

1. Phase tickets (0-8) exist under this epic and are dependency-linked in sequence.
2. Each phase ticket includes implementation instructions and explicit exit criteria.
3. Final structure splits major responsibilities into cohesive modules under `interpreter/env/` (or a nearby focused subdirectory) and reduces `Environment.ts` to orchestration/composition responsibilities.
4. Final validation command passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

