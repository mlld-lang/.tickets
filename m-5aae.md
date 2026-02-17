---
id: m-5aae
status: closed
deps: []
created: 2026-02-12T02:52:36Z
type: bug
priority: 0
assignee: Adam
tags: [interpreter, code-blocks, parameter-binding]
updated: 2026-02-12T15:09:25Z
---
# Code block parameter binding fails inside exe blocks

sh/cmd/js/py/node {} blocks don't receive exe parameters when used inside exe block bodies. Parameters ARE bound for direct exe assignments (exe @fn(x) = sh {...}) but NOT inside exe blocks (exe @fn(x) = [ let @y = sh {...} ]). Shell variables come through as empty strings with no error.

## Design

Review all code block execution contexts (sh, cmd, js, py, node) for consistent parameter binding. Parameters should be available in the same way regardless of whether the code block appears in:
- Direct exe assignment: exe @fn(x) = sh {...}
- Let inside exe block: exe @fn(x) = [ let @y = sh {...} ]
- Let inside for body: for @item in @list [ let @y = sh {...} ]
- Nested blocks: exe @fn(x) = [ if @cond [ let @y = sh {...} ] ]

Reproduce:
  exe @direct(x) = sh {echo "$x"}
  exe @block(x) = [ let @y = sh {echo "$x"} => @y ]
  show @direct("hello")   >> prints 'hello'
  show @block("hello")    >> prints '' (BUG)

## Acceptance Criteria

1. All code block types (sh/cmd/js/py/node) bind parameters identically in direct exe assignments and exe block bodies
2. Existing tests pass
3. New test cases cover parameter binding in all nesting contexts
4. No silent failures — if parameter binding can't work in a context, emit a clear error

