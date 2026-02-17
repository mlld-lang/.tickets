---
id: m-53de
status: closed
deps: []
created: 2026-02-16T00:52:08Z
type: bug
priority: 0
assignee: Adam
updated: 2026-02-16T01:41:27Z
---
# Fix NaN truthiness to be falsy

## Problem

QA found that NaN is treated as truthy at runtime, but docs say it should be falsy (matching JS convention).

The main truthiness function in `interpreter/eval/when/condition-utils.ts` at lines 200-202 already has `return value !== 0 && !isNaN(value)` which should make NaN falsy. There may be a second code path that evaluates truthiness without using this function, or the issue may be in how NaN values are represented (e.g., as the string "NaN" rather than numeric NaN).

## What to do

1. Write a test case that assigns NaN to a variable and checks its truthiness in an `if` block and a `when` expression. Run it to reproduce the bug.
2. If `isTruthy()` in `condition-utils.ts` is correct, trace the actual evaluation path for `if @nanValue [...]` to find where truthiness is checked without calling `isTruthy()`.
3. Fix the alternate code path to also treat NaN as falsy.
4. If NaN is represented as the string "NaN", add a check for that in `isTruthy()`.

## Key files

- `interpreter/eval/when/condition-utils.ts` (lines 135-213): `isTruthy()` function
- Look for other truthiness checks: grep for `=== true`, `== true`, `!value`, `Boolean(`, `!!` in the interpreter eval directory

## Acceptance criteria

- `if NaN [show "truthy"]` produces no output (NaN is falsy)
- `when [NaN => "yes"; * => "no"]` produces "no"
- Add test case in `tests/cases/`


**2026-02-16 01:41 UTC:** Fixed NaN truthiness across condition evaluators by treating string 'NaN' (case-insensitive) as falsy in interpreter/eval/when/condition-utils.ts and interpreter/eval/expressions.ts. Added regressions in interpreter/eval/if.test.ts, interpreter/eval/when.test.ts, interpreter/eval/when/condition-utils.test.ts, and tests/cases/slash/when/nan-truthiness. Updated CHANGELOG.md (2.0.0-rc82 Fixed). Verified with npm test (4200 passed, 58 skipped). Commit: fa19b98f7.
