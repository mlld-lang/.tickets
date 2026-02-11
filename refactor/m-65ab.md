---
id: m-65ab
status: closed
deps: [m-ded6]
created: 2026-02-09T07:00:10Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-68f8
tags: [refactor, guard-post-hook, phase-6]
updated: 2026-02-10T10:17:29Z
---
# Refactor Program: Modularize interpreter/hooks/guard-post-hook.ts - Phase 6: Extract helper injection and cloning utilities



Objective:
Isolate helper provisioning and context cloning behavior.

Instructions for the implementing agent:
- In scope: helper executable factory/injection and clone/preview utility extraction.
- Out of scope: guard decision engine logic and retry enforcement logic.
- Extract helper executable factory/injection logic (`opIs`, `opHas*`, `inputHas`, `prefixWith`, `tagValue`, guard input helper attachment).
- Extract clone/preview utilities used by metadata and error reporting.
- Add tests for helper availability and clone/preview behavior across representative input shapes.

Module boundaries:
- Helper module owns helper creation and attachment contracts.
- Clone/preview module owns metadata/error reporting shaping helpers.
- Runtime evaluator consumes both through explicit interfaces.

Preserve behavior checks:
- Helper names/signatures and attachment behavior remain unchanged.
- Helper fallback behavior with existing env bindings remains unchanged.
- Clone/preview output structure for metadata and errors remains unchanged.

## Acceptance Criteria

1. Helper injection and cloning utilities are extracted into focused modules.
2. Helper contracts and clone/preview behavior remain baseline-compatible.
3. Tests cover helper availability and representative clone/preview outputs.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 10:17 UTC:** Refactor slice complete: extracted helper provisioning into interpreter/hooks/guard-post-helper-injection.ts (opIs/opHas/opHasAny/opHasAll/inputHas, prefixWith, tagValue, guard input helper attachment) and extracted clone/preview/value utilities into interpreter/hooks/guard-post-materialization.ts; guard-post-hook now composes these modules and runtime evaluator consumes them through explicit dependency interfaces. Behavior preserved: helper names/signatures unchanged, existing tagValue fallback preserved, post-hook preview semantics remain non-redacted, clone/descriptor shaping unchanged. Tests added: tests/interpreter/hooks/guard-post-helper-injection.test.ts and tests/interpreter/hooks/guard-post-materialization.test.ts. Targeted tests: npx vitest run tests/interpreter/hooks/guard-post-helper-injection.test.ts tests/interpreter/hooks/guard-post-materialization.test.ts tests/interpreter/hooks/guard-post-runtime-actions.test.ts tests/interpreter/hooks/guard-post-runtime-evaluator.test.ts tests/interpreter/hooks/guard-post-hook.test.ts (PASS). Full gate: npm run build && npm test && npm run test:tokens && npm run test:examples (PASS).
