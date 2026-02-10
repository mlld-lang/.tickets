---
id: m-edc5
status: open
deps: [m-5cd8]
links: []
created: 2026-02-09T06:19:05Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-ef4e
tags: [refactor, environment, phase-1, bootstrap]
---
# Refactor Program: Modularize interpreter/env/Environment.ts - Phase 1: Extract bootstrap and dependency wiring

Objective:
Move constructor bootstrap logic into focused initialization helpers while preserving construction behavior.

Instructions for the implementing agent:
- Extract root bootstrap responsibilities from `Environment` constructor into dedicated modules/factories, including:
  - path context normalization
  - security/registry/project-config setup
  - resolver manager creation and default resolver registration
  - import resolver dependency construction for root vs child
  - variable manager dependency construction
- Keep constructor overload and public constructor signature unchanged.
- Keep initialization order unchanged where behavior depends on sequencing.
- Add unit tests for extracted factory/helper functions where practical.

## Acceptance Criteria

1. Constructor body is materially smaller and delegates bootstrap steps to cohesive helpers.
2. Root and child initialization behavior remains unchanged (validated by phase-0 characterization tests).
3. Public constructor signatures stay stable.
4. Exit criteria: full test gate passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

