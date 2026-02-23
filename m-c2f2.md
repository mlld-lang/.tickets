---
id: m-c2f2
status: closed
deps: []
created: 2026-02-22T17:20:08Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [interpreter, when, let]
updated: 2026-02-22T18:48:46Z
---
# when expression leaks raw AST nodes in let object literal declarations

## Current behavior

```mlld
var @score = 95
# This works — evaluates the when expression:
var @rec1 = { grade: when @score [>= 90 => "A"; * => "B"] }

# This is broken — stores raw AST node as the value:
let @rec2 = { grade: when @score [>= 90 => "A"; * => "B"] }
```

`let` objects with `when`/ternary/binary/unary expressions as property values store the raw AST node instead of the evaluated result.

## Root cause

The `var` path uses `var/collection-evaluator.ts` which has explicit handlers for `WhenExpression`, `TernaryExpression`, `BinaryExpression`, and `UnaryExpression` (around line 309). The `let` path uses `data-values/DataValueEvaluator.ts` which has **no handlers** for these node types — they fall through to a `logger.warn` and return the raw AST.

## Fix

Add expression node handlers to `DataValueEvaluator` (or its `CollectionEvaluator`) matching the ones in `var/collection-evaluator.ts`. All four expression types need handling: `WhenExpression`, `TernaryExpression`, `BinaryExpression`, `UnaryExpression`.

