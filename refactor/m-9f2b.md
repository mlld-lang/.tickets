---
id: m-9f2b
status: open
deps: []
links: []
created: 2026-02-09T06:03:26Z
type: epic
priority: 1
assignee: Adam Avenir
tags: [refactor, exec-invocation]
---
# Refactor Program: Modularize interpreter/eval/exec-invocation.ts

Largest target file: `interpreter/eval/exec-invocation.ts` (~4,090 LOC).

This epic coordinates a phased refactor that decomposes exec invocation behavior into manageable modules while preserving runtime behavior and policy/security semantics.

Execution order:
1. Phase 0 through Phase 9 proceed strictly in order.
2. Each phase closes only after meeting its exit criteria.
3. No phase starts until the previous phase is closed.

Cross-phase constraints:
- Keep `evaluateExecInvocation` as the external entrypoint.
- Preserve behavior for command/code/template/data/pipeline/command-ref/resolver execution paths.
- Preserve guard, policy, streaming, structured value, and security descriptor semantics.
- Keep diffs surgical and verify behavior with characterization tests.

## Acceptance Criteria

1. All phase tickets (0-9) exist as children of this epic.
2. All phase tickets are closed in sequence with evidence attached per ticket.
3. Final architecture splits `exec-invocation.ts` responsibilities into focused modules under `interpreter/eval/exec/` (or equivalent cohesive structure).
4. Final validation command passes:
   npm run build && npm test && npm run test:tokens && npm run test:examples

