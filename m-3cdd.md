---
id: m-3cdd
status: closed
deps: []
created: 2026-02-24T20:45:51Z
type: bug
priority: 0
assignee: Adam Avenir
updated: 2026-02-25T00:57:15Z
---
# Unmatched when returns string 'null' instead of actual null

## Bug

When a `when` expression has no matching condition, it returns a value that displays as "null" but has `typeof === 'string'`. Downstream logic that checks for null (e.g., `when @result`) fails silently because the string "null" is truthy.

## Expected Behavior

An unmatched `when` expression must return JavaScript `null`, not the string `"null"`.

## Where to Look

`interpreter/eval/when-expression.ts` — the no-match return paths:
- Line 404-406: empty conditions array → `buildResult(null, env)`
- Line 931: end of function after both passes → `buildResult(null, accumulatedEnv)`
- Line 438-440: bound-condition falsy → `buildResult(null, accumulatedEnv)`

These all pass `null` to `buildResult()`, so the bug is likely in `buildResult()` itself or in whatever consumes its return value and stringifies null somewhere downstream. Trace the return value from `buildResult()` through to where it gets assigned to a variable to find where `null` becomes `"null"`.

## Test

Write a test case where a `when` expression has no matching conditions. The resulting variable must satisfy `result === null` and `typeof result !== 'string'`.

