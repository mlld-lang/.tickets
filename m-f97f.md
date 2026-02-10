---
id: m-f97f
status: open
deps: []
created: 2026-02-10T04:08:33Z
type: bug
priority: 0
assignee: Adam
tags: [p0, structuredvalue, data-boundary]
updated: 2026-02-10T04:08:49Z
---
# Inline object literals as function arguments resolve to empty string

## Summary

When an object literal is passed directly as a function argument (not via a variable), the parameter inside the function receives an empty string instead of the serialized object.

## Repro

```mlld
exe @test(a, b, data) = [
  show `a=@a b=@b data=@data`
]

>> BROKEN: inline object literal
run @test("x", "y", { count: 5 })
>> Output: a=x b=y data=

>> WORKS: pre-assigned variable
var @info = { count: 5 }
run @test("x", "y", @info)
>> Output: a=x b=y data={"count":5}
```

The object literal `{ count: 5 }` produces a valid StructuredValue when assigned to a variable (`var @info = { count: 5 }` gives `{"count":5}`), but when passed directly in the function call it becomes empty string.

## Workaround

Always assign object literals to a variable before passing as function arguments:
```mlld
let @evtData = { file: @filename, count: @items.length }
@logEvent(@runDir, "complete", @evtData)
```

## Impact

High. The natural pattern `@fn(@x, "label", { key: @val })` silently produces wrong results (empty data) with no error. This is especially dangerous because it doesn't throw — it just loses the data.

## Root Cause Hypothesis

Per `docs/dev/DATA.md`: object literals produce StructuredValues with `.type = 'object'`, `.text = '{"count":5}'`, `.data = {count: 5}`. When the literal appears directly in a function argument position, the AST evaluation path for "object literal as exe argument" likely doesn't wrap the result in a StructuredValue, or the parameter binding drops it.

The fact that this works via variable but not inline suggests the issue is in how exe argument expressions are evaluated — the variable path goes through variable resolution (which preserves StructuredValue wrappers), while the inline literal path may evaluate to a raw value that gets lost during parameter binding.

Key files per DATA.md:
- `interpreter/utils/structured-value.ts`
- Exe argument evaluation and parameter binding
- Object literal evaluation in expression context

## References

- `docs/dev/DATA.md` — StructuredValue contract, object literal wrapping
- `docs/dev/DATA.md:89` — "Object literal: `{type: 'object', text: '{\"a\":1}', data: {a: 1}, mx: {...}}`"

