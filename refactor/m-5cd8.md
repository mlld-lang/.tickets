---
id: m-5cd8
status: open
deps: []
links: []
created: 2026-02-09T06:19:05Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-ef4e
tags: [refactor, environment, phase-0]
---
# Refactor Program: Modularize interpreter/env/Environment.ts - Phase 0: Baseline and characterization

Objective:
Establish a behavior baseline and refactor safety net for `Environment` before moving code.

Instructions for the implementing agent:
- Inventory `Environment` responsibilities and publish a method-to-responsibility map in ticket notes.
- Add/expand characterization tests that cover high-risk behavior clusters:
  - root vs child environment inheritance (reserved names, allowed tools, policy context, import guard scope)
  - resolver variable resolution/caching and reserved resolver behavior (`@debug`, `@input`, keychain denial)
  - effect emission behavior (doc suppression during import, break flushing, security capability attachment)
  - command/code execution wrappers (option merging, ambient `mx` injection for js/node only)
  - child creation + merge behavior (`createChild`, `createChildEnvironment`, block-scoped merge exclusions)
  - cleanup behavior for shadow envs, streaming bridge, and child environments
- Capture baseline risks and proposed extraction boundaries in this ticket.
- Do not perform structural refactors in this phase.

## Acceptance Criteria

1. Characterization tests cover the high-risk clusters listed above and pass.
2. No intentional runtime semantic change is introduced.
3. Baseline architecture notes are attached to this ticket.
4. Exit criteria: full test gate passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

