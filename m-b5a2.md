---
id: m-b5a2
status: closed
deps: []
created: 2026-02-02T22:11:58Z
type: task
priority: 0
assignee: Adam
updated: 2026-02-12T14:52:27Z
---
# Better error message for spread operator typo (.. vs ...)

## Issue

Typing `..@var` instead of `...@var` gives a confusing parse error.

## Reproduction

```mlld
var @run = { id: "test" }
var @newRun = { ..@run, modified: true }
```

## Current Error

```
Expected end of input but " " found.
```

Points at a space character, not helpful.

## Expected

Something like: "Unknown operator '..' - did you mean '...' (spread operator)?"

## Impact

Common typo, especially when LLMs generate code.

