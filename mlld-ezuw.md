---
id: mlld-ezuw
status: done
deps: []
links: []
created: 2026-01-11T16:34:41.902442-08:00
type: task
priority: 2
resolved: 2026-01-25
---
# Ternary operator doesn't work in let assignments inside exe blocks

**RESOLVED**: Fixed in envsec branch.

## Fix Summary

Three changes were needed:

1. **Grammar: TernaryBranch** (`grammar/base/unified-expressions.peggy`)
   - Added `CodeExecution` to `TernaryBranch` so `cmd {...}`, `js {...}`, `sh {...}`, etc. can be used in ternary branches

2. **Grammar: DataPropertyValue** (`grammar/patterns/data-values.peggy`)
   - Added `ExpressionWithOperator` to allow ternary expressions in object literal values
   - Moved `DataStringValue` before `ExpressionWithOperator` to prevent `!` in strings from matching operator lookahead

3. **Interpreter: node type handlers** (`interpreter/core/interpreter.ts`)
   - Added handler for `type === 'code'` nodes (js, sh, python, etc.)
   - Fixed `type === 'command'` handler to always execute commands (removed incorrect `hasRunKeyword` check)

## Original Problem

Can't use ternary operator `? :` for conditional assignment in `let` statements inside exe blocks.

**Now works**:
```mlld
exe @process(filter) = [
  let @tiers = @filter ? @filter.split(",") : []
  => @tiers
]
```

Also works in for blocks:
```mlld
var @results = for @i in [1,2,3] [
  let @x = @i == 2 ? "two" : cmd {echo "not two"}
  => @x
]
```

And in object literals:
```mlld
var @obj = { a: @b ? @b : @c }
```


