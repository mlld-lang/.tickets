---
id: m-7b5b
status: open
deps: []
links: []
created: 2026-02-09T06:24:51Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-d11b
tags: [refactor, var, phase-0]
---
# Refactor Program: Modularize interpreter/eval/var.ts - Phase 0: Baseline and characterization

Objective:
Establish a behavior baseline and safety net for `prepareVarAssignment` and `evaluateVar`.

Instructions for the implementing agent:
- Build a responsibility map for `interpreter/eval/var.ts` with function clusters and current call flows.
- Add/expand characterization coverage for high-risk behavior clusters:
  - identifier extraction and assignment metadata setup
  - lazy vs eager handling for object/array/template values
  - command/code/exec invocation/new expression and env-expression RHS paths
  - variable reference field access, with-tail handling, and condensed pipelines
  - security descriptor collection/merge and capability metadata propagation
  - tool collection validation and tool-subset enforcement in `new ... with { tools: ... }`
  - pipeline skip conditions and post-pipeline variable rewriting
- Capture explicit extraction boundaries for upcoming phases in ticket notes.
- Do not perform structural refactors in this phase.

## Acceptance Criteria

1. Characterization coverage exists for each high-risk cluster listed in the phase instructions.
2. Baseline notes include extraction seams and explicit regression risk callouts.
3. No intentional runtime semantic change is introduced.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

