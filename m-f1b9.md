---
id: m-f1b9
status: closed
deps: []
created: 2026-02-22T17:20:22Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [interpreter, when, expressions]
updated: 2026-02-22T18:48:47Z
---
# Consolidate == equality into single implementation

## Problem

Two separate equality implementations with different rules:
- `isEqual()` in `expressions.ts` — used by expression evaluation
- `compareValues()` in `when/condition-utils.ts` — used by when conditions

`compareValues` has an `isTruthy` catch-all that `isEqual` doesn't, so the same comparison (`0 == false`) can give different results depending on whether it appears in an expression vs a `when` condition.

## Fix

1. Make `isEqual()` in `expressions.ts` the single source of truth for equality.
2. Make `compareValues()` delegate to `isEqual()` instead of reimplementing equality logic.
3. **Remove the `isTruthy` catch-all** from `compareValues` — it silently changes equality semantics when the right-hand side is boolean.
4. Keep the current `isEqual` coercion rules (they are intentional):
   - `"5" == 5` → true (string-to-number coercion)
   - `"true" == true` → true (string-to-boolean coercion)
   - `true == 1` → false (no boolean-to-number coercion)
   - `null == undefined` → true

