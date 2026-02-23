---
id: m-9e5c
status: closed
deps: []
created: 2026-02-22T17:20:39Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [cli, validate]
updated: 2026-02-22T18:48:46Z
---
# Anti-pattern detection too broad — triggers on variable names containing 'json'

## Current behavior

`mlld validate` flags `@json_result` (and similar variable names containing "json") as a deprecated `@json` transform anti-pattern. This is a false positive — the user just named their variable `@json_result`.

## Fix

The anti-pattern detection for `deprecated-json-transform` in `cli/commands/analyze.ts` matches too broadly. Narrow the regex/pattern so it only flags actual `@json` transform usage (like `@json(@var)` or piped `| json`), not variable names that happen to contain the substring "json".

