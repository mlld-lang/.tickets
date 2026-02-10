---
id: m-27bd
status: open
deps: [m-899d]
created: 2026-02-09T07:03:19Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-04d8
tags: [refactor, exe-evaluator, phase-5]
updated: 2026-02-09T07:03:19Z
---
# Refactor Program: Modularize interpreter/eval/exe.ts - Phase 5: Extract executable definition builders (control-flow subtype families)



Objective:
Separate control-flow expression subtype builders.

Instructions for the implementing agent:
- Extract builders for when/foreach/for/loop/exeBlock subtype branches and special language markers.
- Preserve validation checks on AST node type expectations and error messaging.
- Add tests for each control-flow subtype and invalid-content failures.

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
