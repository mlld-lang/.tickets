---
id: m-6f9d
status: closed
deps: [m-5cee]
created: 2026-02-09T07:01:17Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-225c
tags: [refactor, content-loader, phase-5]
updated: 2026-02-10T16:02:52Z
---
# Refactor Program: Modularize interpreter/eval/content-loader.ts - Phase 5: Extract section and heading utilities



Objective:
Isolate section-listing and section-extraction logic.

Instructions for the implementing agent:
- In scope: section name/list extraction, heading fallback matching, rename-template interpolation helpers.
- Out of scope: file/url/glob loading logic and finalization logic.
- Extract section helpers (`extractSectionName`, heading fallback extraction, available section listing, heading transforms) into a dedicated section module.
- Preserve llmxml-first then regex-fallback behavior and rename-template interpolation semantics.
- Add tests for heading-level section lists, rename templates, and missing-section diagnostics.

Module boundaries:
- Section utility module owns section extraction/listing and heading matching.
- Rename-template helper is colocated with section utilities and has no loader I/O dependencies.
- Loaders call section utilities through explicit interfaces.

Preserve behavior checks:
- llmxml-first then regex-fallback matching remains unchanged.
- Heading-level section listing behavior remains unchanged.
- Rename-template interpolation semantics remain unchanged.
- Missing-section diagnostics remain unchanged.

## Acceptance Criteria

1. Section and heading utilities are extracted into a dedicated module.
2. Matching, rename-template, and diagnostics behavior remain baseline-compatible.
3. Tests cover heading lists, fallback matching, and missing-section paths.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 16:02 UTC:** --dir refactor
