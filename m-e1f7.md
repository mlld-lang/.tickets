---
id: m-e1f7
status: closed
deps: []
created: 2026-02-25T19:58:56Z
type: bug
priority: 0
assignee: codex
updated: 2026-02-27T06:48:40Z
---
# @now resolver ignores custom format strings

core/resolvers/builtin/NowResolver.ts:77-78 — Custom date format strings are silently ignored, always returning ISO format instead. Users who specify a format get the wrong output with no indication.

