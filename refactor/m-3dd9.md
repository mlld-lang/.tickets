---
id: m-3dd9
status: open
deps: [m-ce49]
created: 2026-02-09T06:59:36Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-943f
tags: [refactor, import-directive-evaluator, phase-2]
updated: 2026-02-09T06:59:36Z
---
# Refactor Program: Modularize interpreter/eval/import/ImportDirectiveEvaluator.ts - Phase 2: Extract MCP import flow and tool variable factory



Objective:
Modularize MCP-specific import behavior.

Instructions for the implementing agent:
- In scope: MCP tool index/resolution, server spec resolution, param mapping, and MCP variable factory extraction.
- Out of scope: non-MCP resolver/module/node/file/url/dir handlers.
- Extract MCP tool indexing/resolution (`buildMcpToolIndex`, `resolveMcpTool`, param mapping) and server spec resolution into dedicated helpers.
- Extract MCP tool variable creation and binding collision checks into a focused MCP import service.
- Preserve alias collision semantics, namespace import behavior, and executable metadata shape for imported MCP tools.

Module boundaries:
- MCP resolver module owns tool indexing/resolution and parameter mapping.
- MCP import service owns variable creation and binding collision enforcement.
- Top-level evaluator routes MCP imports through these services only.

Preserve behavior checks:
- MCP alias resolution and collision behavior remain unchanged.
- Namespace import behavior for MCP tools remains unchanged.
- Tool executable metadata shape remains unchanged.
- MCP error semantics remain unchanged.

## Acceptance Criteria

1. MCP flow responsibilities are extracted into dedicated modules.
2. Alias/collision semantics and executable metadata remain baseline-compatible.
3. Tests cover MCP tool resolution, param mapping, and binding behaviors.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
