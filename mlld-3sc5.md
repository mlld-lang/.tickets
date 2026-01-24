---
id: mlld-3sc5
status: closed
deps: []
links: []
created: 2026-01-03T02:59:03.873239-08:00
type: bug
priority: 1
---
# Regression: Nested exe calls fail with 'isPipelineInput2 is not a function'

## Bug

Nested exe invocations fail with internal error.

## Reproduction

```mlld
exe @inner(x) = `processed: @x`
exe @outer(prompt) = @prompt | cmd { echo 'done' }

>> This should work but fails:
var @result = @outer(@inner('test'))
```

## Error
```
Error: isPipelineInput2 is not a function
```

## Expected
Nested exe calls should work - evaluate inner first, pass result to outer.

## Status (2026-01-03)

Initial investigation (opus) couldn't reproduce with simple test cases. The isPipelineInput import was fixed (commit 72d7b33c3), but user notes this may fail in specific contexts.

**Action needed:** Re-investigate in the context of the cookbook example where this was originally discovered. The agent working on the cookbook (mlld-i484) filed this, so test with the actual cookbook patterns.

## Notes
- User reports this worked before - likely a regression
- Workaround: use intermediate let variable
```mlld
let @prompt = @inner('test')
var @result = @outer(@prompt)
```

Discovered while working on codebase audit cookbook (mlld-i484).


