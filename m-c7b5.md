---
id: m-c7b5
status: closed
deps: [m-5acf]
links: []
created: 2026-02-24T10:38:21Z
type: task
priority: 1
assignee: Adam
tags: [docs, build]
updated: 2026-02-24T11:21:44Z
---
# Migrate docs build pipeline to mlld

Replace the JS-based docs build with mlld scripts for both website docs and LLM docs. See docs/dev/HANDOFF-DOCS-BUILD.md for full context and status.


## Notes

**2026-02-24T11:21:41Z** Completed:
- Rewrote llm/run/llmstxt.mld in pure mlld (reads categories.json, no Node.js helpers)
- Aligned LLM docs with website page structure (language-reference, flow-control, cli, etc.)
- Renamed docs/src/atoms/control-flow/ → flow-control/
- Updated categories.json as single source of truth for both builds
- Updated convertDocs.js to import from shared categories.json
- Wired build:docs into npm run build (build-quiet.js + build-incremental.js)
- Deleted 8 stale per-category .mld scripts and orphaned build-docs.js
- Removed old output files (llms-syntax.txt, llms-commands.txt, llms-control-flow.txt)
- Updated llms.txt module index and DOCS-LLM.md dev docs
- Found and filed two P0 bugs during implementation (m-b9a2, m-3870) — both fixed
