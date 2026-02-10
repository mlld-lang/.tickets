---
id: m-bfa9
status: open
deps: [m-408b]
links: []
created: 2026-02-09T06:19:05Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-ef4e
tags: [refactor, environment, phase-7, config, diagnostics]
---
# Refactor Program: Modularize interpreter/env/Environment.ts - Phase 7: Extract runtime configuration and diagnostics services

Objective:
Separate runtime configuration toggles and diagnostics paths from core environment orchestration.

Instructions for the implementing agent:
- Extract runtime-configuration methods into dedicated services/modules:
  - URL/path/streaming/provenance toggles
  - local module configuration
  - ephemeral mode reconfiguration flow
- Extract diagnostics/support utilities:
  - collected error formatting/display
  - directive trace stack handling and timing
  - source cache access
- Preserve externally observable behavior and debug/provenance event emission.

## Acceptance Criteria

1. Runtime configuration and diagnostics concerns are moved to focused modules.
2. Ephemeral mode and local module setup behavior remain unchanged.
3. Trace/debug/error-display behavior remains unchanged in tests.
4. Exit criteria: full test gate passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

