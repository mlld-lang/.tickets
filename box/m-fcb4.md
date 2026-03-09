---
id: m-fcb4
status: closed
deps: []
links: [m-d4e5]
created: 2026-03-04T06:53:54Z
type: bug
priority: 0
assignee: Adam
tags: [grammar, show, field-access]
updated: 2026-03-04T07:24:54Z
---
# show directive does not support field access on file-load expressions

## Reproduction

```mlld
show <@root/task.md>.mx.diff
```

Parse error at offset 18 (right after `>`). The `.mx.diff` suffix is not consumed.

## Expected

Should parse and evaluate — `<file>.mx.field` works in `var` RHS and template contexts.

## Root Cause

The `show` directive in `grammar/directives/show.peggy` (line ~172) uses `AlligatorExpression` directly, which only captures `SpacedOrCondensedPipeChain?` after the closing `>`. It does NOT use `AlligatorWithFields` which adds `AnyFieldAccessOptional+` after the expression.

Contexts where `<file>.mx.field` works:
- `var @x = <file>.mx.field` — `var-rhs.peggy` uses `AlligatorWithFields`
- `` show `<file>.mx.field` `` — template interpolation uses `FileFieldChain`
- `<<<file>.mx.field>>` — escaped angle brackets use `FileFieldChain`

## Fix

Add an `AlligatorWithFields` alternative to `show.peggy`, positioned BEFORE the existing `AlligatorExpression` alternative (PEG ordered choice). Similar to how `var-rhs.peggy` line ~24 orders `AlligatorWithFields` before `AlligatorExpression`.

## File

`grammar/directives/show.peggy` — add new alternative before line ~172
