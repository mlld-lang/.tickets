---
id: m-65ab
status: open
deps: [m-ded6]
created: 2026-02-09T07:00:10Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-68f8
tags: [refactor, guard-post-hook, phase-6]
updated: 2026-02-09T07:00:11Z
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
