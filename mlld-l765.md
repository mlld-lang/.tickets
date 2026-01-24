---
id: mlld-l765
status: open
deps: []
links: []
created: 2026-01-04T14:04:17.539753-08:00
type: bug
priority: 2
---
# LLMs misuse let for temp variables in blocks - needs better error/docs

## Issue

LLMs (and likely humans) confuse `let` with JavaScript-style block-scoped temp variables.

They write:
```mlld
exe @buildReport(results) = [
  let @lines = for @r in @results => @r.name
  => @lines.join("\n")
]
```

But `let` is specifically for **mutable** variables inside blocks, not general temp vars.

## Current behavior
Parse error with confusing message about `=>` or `=`

## Suggestions
1. Better error message when `let` is misused this way
2. Add explicit "let is NOT for temp variables" note in LLM docs
3. Consider if we want a different syntax for block-local immutable bindings


