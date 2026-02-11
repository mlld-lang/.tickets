---
id: m-5cee
status: closed
deps: [m-4aaa]
created: 2026-02-09T07:01:17Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-225c
tags: [refactor, content-loader, phase-4]
updated: 2026-02-10T15:52:53Z
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

**2026-02-10 15:52 UTC:** --dir refactor

**2026-02-10 15:53 UTC:** Completed extraction into single-file/glob/security-metadata modules; delegated file/glob branches from interpreter/eval/content-loader.ts; added glob ordering/filtering and all-files-missing-section skip tests in interpreter/eval/content-loader.characterization.test.ts. Checklist complete: metadata wiring parity, glob filtering/ordering parity, section list + missing-section skip parity, rename handling unchanged. Tests: npm test -- interpreter/eval/content-loader*.test.ts (pass); npm run build && npm test && npm run test:tokens && npm run test:examples (pass, checkpoint); npm run build && npm test && npm run test:tokens && npm run test:examples (pass, phase-exit rerun). Commit: 89a89a320.
