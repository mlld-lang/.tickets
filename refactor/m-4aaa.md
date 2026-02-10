---
id: m-4aaa
status: open
deps: [m-e149]
created: 2026-02-09T07:01:17Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-225c
tags: [refactor, content-loader, phase-3]
updated: 2026-02-09T07:01:17Z
---
# Refactor Program: Modularize interpreter/eval/content-loader.ts - Phase 3: Extract URL and HTML conversion handlers



Objective:
Modularize URL fetch + HTML-to-markdown pathways.

Instructions for the implementing agent:
- In scope: URL loading and HTML conversion pathway extraction with section/pipeline support.
- Out of scope: file/glob handlers and finalization adapters.
- Extract URL loading and HTML conversion (`convertHtmlToMarkdown`) with section/pipeline support into a URL handler module.
- Preserve URL metadata attachment, response handling, and text/object return behavior.
- Add tests for HTML conversion fallback paths and URL section extraction behavior.

Module boundaries:
- URL handler module owns fetch/response handling and metadata attachment.
- HTML conversion helper owns HTML-to-markdown transformation and fallback handling.
- Section/pipeline processing is invoked through explicit dependencies.

Preserve behavior checks:
- URL response handling semantics remain unchanged.
- HTML conversion fallback behavior remains unchanged.
- URL metadata and text/object return-shape behavior remain unchanged.
- URL section extraction behavior remains unchanged.

## Acceptance Criteria

1. URL/HTML conversion logic is extracted into focused handler modules.
2. Response, conversion, and return-shape behavior remain baseline-compatible.
3. Tests cover conversion fallbacks and section extraction parity.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
