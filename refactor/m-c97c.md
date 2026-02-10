---
id: m-c97c
status: open
deps: [m-cddf]
created: 2026-02-09T07:02:44Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-845d
tags: [refactor, pipeline-command-execution, phase-4]
updated: 2026-02-09T07:02:44Z
---
# Refactor Program: Modularize interpreter/eval/pipeline/command-execution.ts - Phase 4: Extract guard and policy preflight orchestration



Objective:
Isolate pre-execution guard and policy orchestration with no change to deny/allow labeling behavior.

Instructions for the implementing agent:
- In scope: extract pre-execution orchestration for guard input materialization, pre-hook execution, and policy label-flow validation.
- Out of scope: command/provider/code/node/template/ref execution logic and parameter binding internals.
- Move guard preflight orchestration into `.../preflight/guard-preflight.ts` and policy preflight orchestration into `.../preflight/policy-preflight.ts` (or equivalent).
- Keep deny/reject outcomes and fallback behavior identical to baseline.
- Add tests for guard pre-hook denial paths, when-expression fallback behavior, policy label-flow enforcement, and output descriptor initialization.

Module boundaries:
- Guard preflight module returns normalized guard decision/state plus preflight context.
- Policy preflight module validates input/output policy transitions and returns descriptor data used by downstream handlers.
- Neither module executes branch handlers directly.

Preserve behavior checks:
- Guard preflight outcomes (allow/deny/fallback) match baseline for equivalent inputs.
- Policy labels/descriptors propagate with the same shape across command/code/node flows.
- Denial and policy violation error categories remain unchanged.
- Preflight output contracts consumed by later handlers stay stable.

## Acceptance Criteria

1. Guard and policy preflight orchestration is extracted into focused modules with explicit interfaces.
2. Label-flow validation, descriptor setup, and guard deny/fallback semantics remain unchanged.
3. Preserve-behavior checks are covered by focused tests.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
