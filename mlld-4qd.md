---
id: mlld-4qd
status: closed
deps: []
links: []
created: 2025-12-15T14:30:22.329257-08:00
type: bug
priority: 0
---
# run syntax not supported in exe/for blocks

## Problem

The grammar doesn't support `run` forms inside exe blocks and for blocks:
- `run @func()` - execute function via run
- `run cmd {command}` - shell commands
- `run js {code}`, `run node {code}`, `run py {code}`, `run sh {code}`, `run bash {code}` - language executors

This is a major gap since blocks are now the primary way to write multi-statement logic in mlld.

## Expected behavior

All run forms should work inside `[...]` blocks:

```mlld
exe @process() = [
  let @data = run cmd {curl https://api.example.com}
  let @parsed = run js { return JSON.parse(@data) }
  let @result = run @transform(@parsed)
  => @result
]
```

## Current status

Need to check:
1. Which run forms are missing from BlockStatement grammar
2. Whether this affects exe blocks, for blocks, or both
3. Test coverage needed

## Fix location

Likely `grammar/mlld.peggy` - BlockStatement rule needs to include all run forms that work at top level.


