---
id: m-9a8d
status: closed
deps: [m-8be2]
links: []
created: 2026-02-09T06:24:52Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-d11b
tags: [refactor, var, phase-4, execution, reference]
updated: 2026-02-11T04:37:43Z
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

## Wave 2 Phase Guardrails

Required design constraints for this phase:
- Do not create runtime modules under 60 lines unless the file is a pure type-definition file or re-export barrel.
- Do not duplicate shared logic across sibling modules in this area; extract a shared helper/service when behavior overlaps.
- Do not introduce constructor callback-lambda injection as a service-composition strategy; use interface-typed service objects.
- Define shared result/context/types/type-guards once per extraction area and import them instead of re-declaring.
- Keep module dependency direction acyclic in the touched area before closing the phase.

Evidence required in the phase note before close:
- Touched runtime modules with line counts and a short ownership note per module.
- Targeted test commands run for this phase and pass/fail status.
- Explicit statement that no new sub-60 runtime module was introduced (or list allowed type/barrel exceptions).
- Explicit statement that no callback-lambda service injection was introduced (or justify a recursion-only exception).

## Acceptance Criteria

1. Reference and execution branches are extracted behind stable evaluator interfaces.
2. Resolution contexts, command delegation, and tool-scope checks remain unchanged.
3. Added tests cover branch-specific behavior and error paths.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples


**2026-02-11 04:37 UTC:** Implemented Phase 4 extraction for reference + execution RHS branches.

Code changes:
- interpreter/eval/var/reference-evaluator.ts (174 LOC): owns VariableReference + VariableReferenceWithTail evaluation paths, including field access resolution contexts and condensed/with-clause pipeline descriptor hints.
- interpreter/eval/var/execution-evaluator.ts (324 LOC): owns execution-oriented RHS branches (code, command, ExecInvocation, ExeBlock, WhenExpression, ForExpression, LoopExpression, foreach, Directive env, NewExpression), including command-with-withClause delegation to evaluateRun and new-with-tools subset enforcement.
- interpreter/eval/var.ts (1150 LOC): orchestrates via new evaluators; preserves entrypoint signatures and existing variable construction/pipeline finalization behavior.

Tests added:
- interpreter/eval/var/reference-evaluator.test.ts
- interpreter/eval/var/execution-evaluator.test.ts

Sub-checkpoint commands and status:
- npm run build ✅
- npm test -- interpreter/eval/var/reference-evaluator.test.ts interpreter/eval/var/execution-evaluator.test.ts interpreter/eval/var.characterization.test.ts ✅

Full gate command and status:
- npm run build && npm test && npm run test:tokens && npm run test:examples ✅

Guardrail assertions:
- No new runtime module under 60 LOC introduced (new runtime modules: 174 LOC, 324 LOC).
- No callback-lambda service injection introduced; evaluator composition uses typed dependency objects/services.
- No new acyclic dependency violations introduced in interpreter/eval/var* (module edges remain one-way from var.ts orchestration to leaf evaluators).
