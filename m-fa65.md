---
id: m-fa65
status: closed
deps: []
created: 2026-01-31T03:51:25Z
type: bug
priority: 2
assignee: Adam
tags: [error-message, grammar]
updated: 2026-01-31T08:00:28Z
---
# Update outdated when syntax error message

## Current Behavior

When using `when` with a block (imperative style), the error message suggests outdated syntax:

```
Invalid when syntax: missing '=>' before block.

Change:  when  [...]  =>  when  => [...]
```

## Problem

The error message suggests `when => [...]` syntax which doesn't align with current design where:
- `when` is for pattern matching / value-returning switches
- `if` is for imperative blocks

## Triggered By

```mlld
when @failures.length > 0 [
  log "something"
]
```

## Expected

Error should indicate to use `if` for imperative blocks:

```
Use 'if' for imperative blocks, not 'when'.

Change:  when @condition [...]  =>  if @condition [...]
```

## Location

Likely in grammar error handling or a custom error in the parser.


**2026-01-31 08:00 UTC:** Fixed. Error message now reads 'Use if for imperative blocks, not when.' and provides clear examples showing the difference between when (pattern matching) and if (imperative blocks). Updated test case at tests/cases/invalid/when-missing-arrow-before-block/.
