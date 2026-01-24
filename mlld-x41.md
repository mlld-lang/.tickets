---
id: mlld-x41
status: closed
deps: []
links: []
created: 2025-12-09T17:09:42.728504-08:00
type: bug
priority: 0
---
# Field access on JS-returned objects fails in for-loop source

## Reproduction

```mlld
/exe @getData() = js { return { all: ['a','b','c'] } }
/var @result = @getData()
/show "All: @result.all"           # Works: ["a","b","c"]
/for @item in @result.all => show @item  # FAILS: undefined
```

## Issue

Field access `@result.all` works in string interpolation but returns `undefined` when used as a /for loop source.

## Expected

`@result.all` should return the array `['a','b','c']` consistently in all contexts.

## Context

JS function returns object, gets stored as StructuredValue. Field access works for display but not for iteration.

Reported by: partydev.1


