---
id: m-d11b
status: open
deps: []
links: []
created: 2026-02-09T06:24:51Z
type: epic
priority: 1
assignee: Adam Avenir
tags: [refactor, var]
---
# Refactor Program: Modularize interpreter/eval/var.ts

Largest unplanned target file: `interpreter/eval/var.ts` (~2,418 LOC, ~94 KB).

This epic coordinates a phased refactor that decomposes `/var` assignment evaluation into focused modules while preserving runtime semantics, security descriptor propagation, tool scope enforcement, and pipeline behavior.

Execution order:
1. Phase 0 through Phase 9 proceed strictly in order.
2. Each phase closes only after its exit criteria are met.
3. No phase starts until its dependency ticket is closed.

Cross-phase constraints:
- Keep `prepareVarAssignment` and `evaluateVar` signatures stable.
- Preserve lazy vs eager behavior for arrays, objects, template wrappers, file/content loaders, command execution, and variable references.
- Preserve security descriptor propagation, capability context generation, and variable metadata attachment.
- Preserve tool collection normalization and tool-subset enforcement semantics for `new ... with { tools: ... }`.
- Keep diffs surgical and backed by characterization tests before structural moves.

## Acceptance Criteria

1. Phase tickets (0-9) exist under this epic and are dependency-linked in sequence.
2. Each phase ticket includes explicit implementation instructions and explicit exit criteria.
3. Final structure splits major `var.ts` responsibilities into cohesive modules under `interpreter/eval/var/` (or an equivalent focused subdirectory), and reduces `var.ts` to orchestration/composition responsibilities.
4. Final validation command passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

