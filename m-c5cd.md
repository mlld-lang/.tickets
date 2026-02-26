---
id: m-c5cd
status: closed
deps: []
created: 2026-02-23T16:43:56Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [interpreter, js]
updated: 2026-02-24T18:27:30Z
---
# Inline var = js {} returns strings instead of typed values

## Current behavior

```mlld
var @x = js { return [] }   # @x is string '[]' (truthy!)
var @y = js { return {} }   # @y is string '{}' (truthy!)
var @z = js { return 42 }   # @z is string '42'
```

But:

```mlld
exe @fn() = js { return [] }
var @x = @fn()              # @x is array [] (falsy, correct)
```

## Root cause

Both paths use JavaScriptExecutor which always returns strings. The exe path in `interpreter/eval/exec/code-handler.ts` (lines 318-336) has a JSON re-parse step that recovers typed values. The inline var path in `interpreter/eval/code-execution.ts` does NOT have this step.

## Fix

Add the same JSON re-parse logic from code-handler.ts:318-336 to code-execution.ts after the executeCode call. This ensures `var @x = js { return [] }` produces a real array, not the string '[]'.

## Impact

Breaks documented truthiness contract — docs say [] and {} are falsy, but inline js makes them truthy strings. @typeof returns 'string' for everything from inline js.

