---
id: m-d9be
status: closed
deps: [m-225c]
created: 2026-02-10T06:55:15Z
type: epic
priority: 0
assignee: Adam Avenir
tags: [refactor, program-master-epic]
updated: 2026-02-10T17:12:28Z
---
# Refactor Program: Master Epic - Coordinated Modularization Execution

Purpose:
Coordinate all refactor epics under one execution plan with strict quality gates at every phase and sub-phase.

Program scope:
- `m-c494` `interpreter/hooks/guard-pre-hook.ts`
- `m-68f8` `interpreter/hooks/guard-post-hook.ts`
- `m-845d` `interpreter/eval/pipeline/command-execution.ts`
- `m-9747` `interpreter/core/interpreter.ts`
- `m-943f` `interpreter/eval/import/ImportDirectiveEvaluator.ts`
- `m-a97c` `interpreter/eval/import/VariableImporter.ts`
- `m-225c` `interpreter/eval/content-loader.ts`

## Global Execution Rules (Mandatory)

1. Work strictly in listed order below unless this ticket is updated with an explicit exception.
2. Complete one phase at a time; do not overlap phases.
3. Treat every micro-checklist item inside a phase as a sub-phase checkpoint.
4. At each sub-phase checkpoint:
   1. Run targeted tests for touched behavior.
   2. Run full gate:
      `npm run build && npm test && npm run test:tokens && npm run test:examples`
   3. Fix all failures before commit.
   4. Commit only after green results.
5. At each phase boundary:
   1. Re-run full gate:
      `npm run build && npm test && npm run test:tokens && npm run test:examples`
   2. Fix all failures before closing phase ticket.
   3. Do not start next phase until the current phase is closed.
6. Do not start next epic until all phases in current epic are closed.
7. Keep refactor work behavior-preserving; feature changes require a separate ticket.

## Cross-Epic Execution Order

1. Guard Pre-Hook Refactor (stabilize guard decision core first)
   - Epic: `m-c494`
   - Phase order: `m-5156 -> m-0635 -> m-1b94 -> m-83af -> m-b451 -> m-6a4f -> m-6a79 -> m-d121 -> m-6588`

2. Guard Post-Hook Refactor (align after-guard enforcement with pre-guard contracts)
   - Epic: `m-68f8`
   - Phase order: `m-719d -> m-70f1 -> m-735c -> m-2136 -> m-33c8 -> m-ded6 -> m-65ab -> m-3b09`

3. Pipeline Command Execution Refactor (high branch-parity surface)
   - Epic: `m-845d`
   - Phase order: `m-fe13 -> m-9edc -> m-abb7 -> m-cddf -> m-c97c -> m-a69c -> m-c8ba -> m-7a82`

4. Core Interpreter Refactor (dispatch/resolution/orchestration core)
   - Epic: `m-9747`
   - Phase order: `m-ce57 -> m-11c8 -> m-fd91 -> m-8659 -> m-2e93 -> m-1078 -> m-5bf5 -> m-f79f`

5. Import Directive Evaluator Refactor (broad import branch matrix)
   - Epic: `m-943f`
   - Phase order: `m-3469 -> m-ce49 -> m-3dd9 -> m-4c1a -> m-118a -> m-5b60 -> m-a2a7 -> m-2f95`

6. Variable Importer Refactor (serialization and executable recovery coupling)
   - Epic: `m-a97c`
   - Phase order: `m-16bd -> m-aa0a -> m-e21c -> m-61c3 -> m-0b66 -> m-5ca6 -> m-86a1 -> m-8aeb`

7. Content Loader Refactor (finalize mixed output-shape stabilization)
   - Epic: `m-225c`
   - Phase order: `m-9251 -> m-493d -> m-e149 -> m-4aaa -> m-5cee -> m-6f9d -> m-031c -> m-03cd`

## Program Completion Criteria

1. All listed epics and all their phase tickets are closed in listed order.
2. Every phase and sub-phase includes evidence of test execution and fixes before commit/progression.
3. No open regression in build, unit/integration tests, token tests, or example tests at program close.

**2026-02-10 17:12 UTC:** --dir refactor
