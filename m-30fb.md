---
id: m-30fb
status: closed
deps: []
created: 2026-02-19T20:01:26Z
type: bug
priority: 0
assignee: Adam Avenir
updated: 2026-02-19T23:21:24Z
---
# Change @parse.llm failure return from false to null

## Problem

`@parse.llm` returns `false` (boolean) when it cannot extract JSON from input. This is inconsistent with other `@parse` variants (`@parse.strict`, `@parse.loose`) which throw on failure, and causes misleading downstream errors.

### Real incident

The review orchestrator (`llm/run/review/index.mld`) used `@result | @parse.llm` then accessed `@resultData.verdict`. When parse failed, `@resultData` was `false`, producing:
```
Cannot access field "verdict" on non-object value (boolean) at line 456
```
The actual root cause — "couldn't extract JSON from LLM output" — was completely hidden.

### Why `false` is wrong

1. **Inconsistent**: `@parse.strict` and `@parse.loose` throw on failure. `@parse.llm` silently returns `false`. Three parse variants, two error contracts.
2. **Misleading errors**: Real problem is "parse failed" but user sees "can't access .field on boolean" downstream.
3. **Bad guard ergonomics**: `false` is truthy-like conceptually but falsy — confusing. Standard `if !@result` guards happen to work but the intent is unclear. `null` is the conventional "no value" sentinel.

## Fix

Change `@parse.llm` to return `null` instead of `false` on failure.

### Location

`interpreter/builtin/transformers.ts` — the `'llm'` mode branch (around line 30) returns `false`. Change to `null`.

### Future consideration

Longer term, consider making `@parse.llm` throw by default (consistent with other variants) and supporting safe piping (`@data | @parse.llm?`) for graceful handling. But `null` is the right immediate fix with minimal blast radius.

## Acceptance Criteria

- [ ] `@parse.llm` returns `null` (not `false`) when JSON extraction fails
- [ ] Existing tests updated for null return
- [ ] Error message when accessing .field on null result is clearer than the boolean case
- [ ] No breaking changes to callers using `if !@result` guards (both false and null are falsy)

