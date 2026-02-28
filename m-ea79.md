---
id: m-ea79
status: closed
deps: []
created: 2026-02-17T01:42:11Z
type: bug
priority: 2
assignee: codex
updated: 2026-02-27T06:48:40Z
---
# Markdown-mode validator picks up :: and ::: template delimiters in prose

The mlld validate tool in markdown mode parses `::` and `:::` as template delimiters even when they appear in regular prose text (outside code fences). Example: in `docs/src/atoms/syntax/escaping-at.md`, a bullet list mentioning double-colon templates causes a parse error at the `::` in the prose. The markdown-mode parser should only parse mlld syntax inside code fences, not in surrounding markdown text.

## Acceptance Criteria

Markdown-mode validation of escaping-at.md passes without errors when :: appears in prose

