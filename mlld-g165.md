---
id: mlld-g165
status: closed
deps: []
links: []
created: 2026-01-03T02:59:03.553862-08:00
type: task
priority: 2
assignee: pen
---
# Docs: Explain block variable immutability earlier and clearer

Users encounter friction when trying to use expressions directly in block return object literals:

```mlld
>> This fails - looks like syntax error but it's actually immutability
=> { file: @info.name, review: @review.trim() }

>> Must use let for intermediate values
let @filename = @info.name
let @trimmed = @review.trim()
=> { file: @filename, review: @trimmed }
```

The docs should explain earlier and more clearly:
- Variables (except let) in blocks are immutable for security
- This means you can't have complex expressions directly in return object literals
- The pattern is: use let for any computed values, then reference in return

This came up in the codebase audit cookbook example (mlld-i484).


