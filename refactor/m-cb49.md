---
id: m-cb49
status: open
deps: [m-8731]
links: []
created: 2026-02-09T06:19:05Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-ef4e
tags: [refactor, environment, phase-4, effects, context, sdk]
---
# Refactor Program: Modularize interpreter/env/Environment.ts - Phase 4: Extract effects, context wrappers, and SDK bridge

Objective:
Isolate output/effect routing and context-wrapper behavior from core environment state.

Instructions for the implementing agent:
- Extract effect and intent routing logic (`emitEffect`, `intentToEffect`, `emitIntent`, `renderOutput`) into a dedicated output coordinator.
- Extract SDK stream/command bridge mapping and event emission helpers.
- Consolidate thin wrappers around `ContextManager` and `GuardRegistry` into a coherent context facade.
- Preserve ordering behavior, import-time doc suppression, break collapsing behavior, and provenance inclusion in SDK events.

## Acceptance Criteria

1. Effect/intention handling and SDK bridge logic are isolated into focused components.
2. Context wrapper behavior (pipeline/guard/operation/tool-call tracking) remains unchanged.
3. Streaming/event and effect-ordering tests remain green.
4. Exit criteria: full test gate passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

