---
id: m-9b7b
status: closed
deps: []
created: 2026-01-31T08:15:52Z
type: bug
priority: 2
assignee: Adam
tags: [env, var, interpreter]
updated: 2026-01-31T08:20:14Z
---
# var RHS env expression does not return value

When using env expression as RHS in var assignment, the env block executes but does not return a value. The grammar parses correctly but var.ts lacks a handler for Directive type values.


**2026-01-31 08:20 UTC:** Fixed by adding a handler in var.ts for Directive nodes with kind === 'env'. The env expression RHS now properly evaluates and captures the return value. Added test case feat/env-expression-var-rhs.
