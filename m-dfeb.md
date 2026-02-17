---
id: m-dfeb
status: closed
deps: []
created: 2026-02-10T04:08:17Z
type: bug
priority: 0
assignee: Adam
tags: [p0, structuredvalue, data-boundary]
updated: 2026-02-12T14:31:46Z
---
# StructuredValue: object spread on function parameters gets .text instead of .data

## Summary

When an object is passed as a function argument and then spread inside the function body, the spread fails because the parameter holds the StructuredValue's `.text` (string serialization) instead of `.data` (parsed object).

## Repro

```mlld
exe @merge(data) = [
  let @result = { ts: @now, ...@data }
  show `@result`
]

var @info = { count: 5 }
run @merge(@info)
```

**Error:** `Cannot spread non-object value from data (got string)`

Debug output shows `@data` is `{"count":5}` (a string), not `{count: 5}` (an object).

## Workaround

Re-parse the parameter before spreading:
```mlld
let @parsed = @data | @json
let @result = { ts: @now, ...@parsed }
```

## Impact

High. The `{ ...@param }` pattern is fundamental to orchestrator state management (e.g., `@logEvent` spreading event data into a timestamped object). Every function that accepts an object and spreads it is affected.

## Root Cause Hypothesis

Per `docs/dev/DATA.md`: function parameters should preserve StructuredValue wrappers. The spread operator likely calls `asText()` instead of `asData()` when resolving the parameter, or the parameter binding itself unwraps to `.text` before the spread can access `.data`.

Key files per DATA.md:
- `interpreter/utils/structured-value.ts`
- `interpreter/env/auto-unwrap-manager.ts`
- Parameter binding in exe evaluation

## References

- `docs/dev/DATA.md` — StructuredValue contract, "Use `asData()` at computation boundaries"
- `docs/dev/DATA.md:218` — "Problem: Function receives string instead of array / Fix: Use asData() where value enters JS execution context"

