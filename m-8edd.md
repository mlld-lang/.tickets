---
id: m-8edd
status: closed
deps: []
created: 2026-02-23T16:43:59Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [docs, gotchas]
updated: 2026-02-25T00:44:19Z
---
# Fix incomplete truthiness table in gotchas doc

## What's wrong

The truthiness table in `docs/src/atoms/mistakes/gotchas.md` (lines 35-48) lists only 6 falsy values. The actual implementation in `interpreter/eval/when/condition-utils.ts` (isTruthy function, lines 104-186) has more.

## Missing from the table

- `undefined`
- `"false"` (the string, case-insensitive)
- `"0"` (the string)
- `NaN`

## Fix

Update the gotchas truthiness table to list ALL falsy values:

| Value | Falsy? |
|-------|--------|
| `false` | yes |
| `0` | yes |
| `NaN` | yes |
| `""` (empty string) | yes |
| `"0"` (string) | yes |
| `"false"` (string) | yes |
| `"NaN"` (string) | yes |
| `null` | yes |
| `undefined` | yes |
| `[]` (empty array) | yes |
| `{}` (empty object) | yes |

Note the JS difference: in JS, [], {}, '0', and 'false' are all truthy.

