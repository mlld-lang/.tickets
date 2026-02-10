---
id: m-c8ba
status: open
deps: [m-a69c]
created: 2026-02-09T07:02:44Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-845d
tags: [refactor, pipeline-command-execution, phase-6]
updated: 2026-02-09T07:02:44Z
---
# Refactor Program: Modularize interpreter/eval/pipeline/command-execution.ts - Phase 6: Extract code/node/template/ref execution handlers



Objective:
Separate non-command execution branches into dedicated handlers with branch-parity guarantees.

Instructions for the implementing agent:
- In scope: extract non-command execution handlers only (`code`, `node`, `template`, `commandRef`).
- Out of scope: command/provider handler, preflight orchestration, and parameter-binding internals.
- Create branch-specific handlers under `.../handlers/execute-code.ts`, `.../handlers/execute-node.ts`, `.../handlers/execute-template.ts`, and `.../handlers/execute-command-ref.ts` (or equivalent).
- Keep branch dispatch ordering and branch selection predicates unchanged.
- Add tests for each branch, including recursion paths for `commandRef`, event-emitter/legacy-stream rejection behavior, and structured wrapping parity.

Module boundaries:
- Each handler owns one branch family and returns a normalized execution result.
- Shared normalization utilities remain outside handlers to avoid cross-branch coupling.
- Orchestrator keeps only dispatch wiring and post-handler aggregation.

Preserve behavior checks:
- Code-language subpaths (`mlld-when`, `foreach`, `for`, `loop`, regular code) keep baseline semantics.
- Node function/class execution keeps invocation semantics and error surface.
- Template execution keeps interpolation/result wrapping contracts.
- `commandRef` recursion and failure behavior remain baseline-compatible.
- Structured output wrapping behavior remains parity-consistent with command/provider branch outputs.

Micro-checklist (high-risk parity matrix):
- Add a branch matrix test file that runs equivalent inputs through `code`, `node`, `template`, and `commandRef` handlers and asserts normalized output-shape parity.
- Cover `code` subpath cases independently: `mlld-when`, `foreach`, `for`, `loop`, and regular code.
- Cover `node` function and class paths, including one failing invocation path each.
- Cover `template` branch with both text and structured intermediate values.
- Cover `commandRef` recursion depth > 1 and one failing recursive path.
- Assert event-emitter and legacy-stream rejection semantics are unchanged per branch.
- Assert descriptor/label propagation parity between non-command handlers and baseline command/provider expectations.
- Assert retry signal passthrough semantics remain unchanged for branches that can surface retry metadata.

## Acceptance Criteria

1. Non-command branches are extracted into dedicated handlers with explicit interfaces.
2. Dispatch semantics, branch parity, and recursion/error behavior remain unchanged.
3. Preserve-behavior checks are covered for each non-command branch.
4. Every item in the micro-checklist is backed by explicit tests before closing this phase.
5. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
