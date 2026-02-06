---
id: m-c96b
status: closed
deps: []
created: 2026-01-30T12:39:50Z
type: bug
priority: 2
assignee: Adam
updated: 2026-01-30T22:44:50Z
---
# Ternary operator not supported in let assignments

## Issue

Ternary operator syntax fails in let assignments:

```mlld
let @existingAnalysis = @existingAnalyses.length > 0 ? @existingAnalyses[@lastIdx] : null
```

Error:
```
Expected ",", ";", ... but "?" found. (line 32:56)
```

## Expected Behavior

Ternary should work as a standard expression form in mlld.

## Workaround

Use `when first`:
```mlld
let @existingAnalysis = when first [@existingAnalyses.length > 0 => @existingAnalyses[@lastIdx]; * => null]
```

## Context

Hit during polish pipeline migration to JSONL polling.

