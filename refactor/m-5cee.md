---
id: m-5cee
status: open
deps: [m-4aaa]
created: 2026-02-09T07:01:17Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-225c
tags: [refactor, content-loader, phase-4]
updated: 2026-02-09T07:01:17Z
---
# Refactor Program: Modularize interpreter/eval/content-loader.ts - Phase 4: Extract file and glob loader services



Objective:
Separate single-file and glob loading implementations.

Instructions for the implementing agent:
- In scope: single-file and glob loader extraction plus descriptor/metadata propagation helpers.
- Out of scope: section-heading utilities and finalization adapters.
- Extract `loadSingleFile` and `loadGlobPattern` along with descriptor attachment and metadata propagation helpers.
- Preserve section list behavior, rename handling, and file metadata wiring (`filename`, `relative`, `absolute`, etc.).
- Add tests for glob filtering/ordering and section-missing skip behavior.

Module boundaries:
- Single-file loader module owns non-glob path loading.
- Glob loader module owns traversal/filtering/ordering and glob metadata behavior.
- Shared metadata helper module owns descriptor attachment and metadata wiring.

Preserve behavior checks:
- File metadata wiring remains unchanged.
- Glob filtering and ordering behavior remain unchanged.
- Section list and section-missing skip behavior remain unchanged.
- Rename handling remains unchanged.

## Acceptance Criteria

1. File and glob loading logic is extracted into focused modules.
2. Metadata wiring and loading semantics remain baseline-compatible.
3. Tests cover glob ordering/filtering and section skip behavior.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
