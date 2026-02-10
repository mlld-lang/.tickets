---
id: m-9a8d
status: open
deps: [m-8be2]
links: []
created: 2026-02-09T06:24:52Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-d11b
tags: [refactor, var, phase-4, execution, reference]
---
# Refactor Program: Modularize interpreter/eval/var.ts - Phase 4: Extract reference and execution evaluators

Objective:
Extract reference and execution RHS evaluators while preserving evaluation semantics.

Instructions for the implementing agent:
- Isolate variable reference paths (`VariableReference`, `VariableReferenceWithTail`) into dedicated helper module(s), including field access, resolution contexts, and condensed pipeline hooks.
- Isolate execution-oriented branches (`command`, `code`, `ExecInvocation`, `ExeBlock`, `WhenExpression`, `ForExpression`, `LoopExpression`, `foreach`, `Directive env`, `NewExpression`) into focused evaluator module(s).
- Preserve command-with-withClause delegation to `evaluateRun` and preserve descriptor-hint flow into pipeline calls.
- Preserve `new ... with { tools: ... }` tool-scope narrowing checks and error conditions.
- Add focused tests for reference-tail behavior, command delegation behavior, and execution branch outputs.

## Acceptance Criteria

1. Reference and execution branches are extracted behind stable evaluator interfaces.
2. Resolution contexts, command delegation, and tool-scope checks remain unchanged.
3. Added tests cover branch-specific behavior and error paths.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

