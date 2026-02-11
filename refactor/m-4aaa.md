---
id: m-4aaa
status: closed
deps: [m-e149]
created: 2026-02-09T07:01:17Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-225c
tags: [refactor, content-loader, phase-3]
updated: 2026-02-10T15:37:12Z
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

**2026-02-10 15:37 UTC:** --dir refactor Completed URL/HTML extraction phase. Changes: moved HTML conversion fallback logic into interpreter/eval/content-loader/html-conversion-helper.ts; moved URL fetch/section/pipeline/object-shape routing into interpreter/eval/content-loader/url-handler.ts with explicit dependencies for section and pipeline helpers; rewired interpreter/eval/content-loader.ts URL branch to consume handler output and keep finalization adapters in orchestrator. Behavior parity kept for URL metadata attachment, section and section-list extraction, HTML conversion fallback, and text/object result shapes. Tests added: URL conversion fallback when Readability returns null, fallback to raw HTML on conversion error, URL section-list extraction parity (interpreter/eval/content-loader-url.test.ts). Tests: targeted PASS (npx vitest run interpreter/eval/content-loader-url.test.ts interpreter/eval/content-loader.characterization.test.ts interpreter/eval/content-loader.test.ts interpreter/eval/content-loader-ast.test.ts interpreter/eval/content-loader-markdown.test.ts interpreter/eval/content-loader-tagging.test.ts interpreter/eval/content-loader.structured.test.ts); full gate PASS x2 (npm run build && npm test && npm run test:tokens && npm run test:examples). Commit: a6be5b48e.
