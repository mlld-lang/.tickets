---
id: m-bfbd
status: closed
deps: []
created: 2026-02-12T18:24:11Z
type: bug
priority: 1
assignee: Adam
tags: [error-messages, dx, ergonomics]
updated: 2026-02-13T15:56:55Z
---
# Error enhancement: common file extensions parsed as field access

When users write backtick template paths like `@runDir/reviews/@filename.json`, the .json is parsed as field access on @filename, silently producing an empty/undefined value. Same for .md, .txt, .mld, .csv, .yaml, .yml, .html, .css, .js, .ts, .py, .sh, .att, .mtt, .jsonl, .xml, .toml, .env, .log, .pdf.

This is a very common trap for new users building orchestrators. The workaround is `@filename\.json` but it's undiscoverable.

Suggested fix: Add error enhancement patterns (per docs/dev/ERRORS.md) that detect when field access on a variable returns undefined and the field name matches a common file extension. The enhanced error should say something like: '@filename.json' looks like field access — if you meant a file extension, escape the dot: '@filename\.json'

Implementation: Add a parse-time or runtime error enhancement pattern in errors/parse/ or errors/js/. Pattern should match: field access returning undefined where field name is a known extension. Could also add a mlld validate warning for .ext patterns in backtick templates where the extension is a common file type.

See: plugins/orchestrate/skills/orchestrator/gotchas.md 'Escaping dots in constructed paths' for the documented workaround.

