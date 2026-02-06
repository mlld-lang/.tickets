---
id: mlld-qyx6
status: closed
deps: []
links: []
created: 2026-01-22T21:29:33.537638-08:00
type: feature
priority: 2
tags: [size-s, complexity-s, risk-m, impl-partial]
---
# Feature: Optional file loading with <path>? postfix syntax

Add optional/soft-fail file loading using postfix `?` syntax outside the angle brackets.

## Syntax

```mlld
var @config = <config.json>?        >> Returns null/undefined if file missing
var @files = <*.json>?              >> Returns [] if no matches (vs error)
var @data = <@base/optional.json>?  >> Works with interpolated paths
```

## Rationale

The `?` should be **outside** the angle brackets because:
1. Inside would conflict with glob wildcards (`?` = match any single character)
2. Follows JavaScript optional chaining pattern (`obj?.prop`)  
3. The `?` modifies the load operation result, not the path itself

## Implementation

- Grammar: Add optional `?` postfix to AlligatorExpression in alligator.peggy
- Interpreter: Wrap load-content in try/catch, return null/[] on failure when `?` present
- AST: Add `optional: true` flag to load-content nodes

## Use Cases

- Loading optional config files
- Glob patterns that may have zero matches
- Defensive loading in scripts that run across different environments


