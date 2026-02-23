---
id: m-4ee7
status: closed
deps: []
created: 2026-02-22T17:21:26Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [docs]
updated: 2026-02-22T18:48:46Z
---
# Fix log howto: @debug example uses reserved name, @log() notation misleads

## What's wrong

The log howto page (`docs/src/atoms/howto/log.md` or similar) has two issues:

1. **Uses `@debug` as a variable name** — `@debug` collides with reserved/builtin names. Rename to `@debugMode` or `@debugFlag` or any non-reserved name.
2. **Shows `@log()` notation** — implies `log` is a callable function. It's a directive. Change any `@log("message")` examples to `log "message"` or `log @variable`.

