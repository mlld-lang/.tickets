---
id: m-f9b2
status: open
deps: []
created: 2026-02-23T16:43:53Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [interpreter, js, builtins]
updated: 2026-02-23T16:44:08Z
---
# js block-body lambdas break implicit return — kills @sortBy/@groupBy

## Current behavior

```mlld
exe @sortBy(array, key) = js {(
  array.slice().sort((a, b) => {
    const aVal = a[key]; const bVal = b[key];
    if (aVal < bVal) return -1;
    if (aVal > bVal) return 1;
    return 0;
  })
)}
```

Returns undefined/empty. The `return -1`, `return 1`, `return 0` inside the .sort() callback are detected by `containsReturnStatement()` which then skips adding an implicit return to the top-level expression. The outer function returns undefined.

Expression-body arrows (`item => item.name`) work fine because they have no `return` keyword.

## Root cause

`containsReturnStatement()` in `interpreter/env/executors/implicit-return.ts` (lines 76-101) walks the ENTIRE AST tree including nested arrow function bodies. It should stop at `ArrowFunctionExpression`, `FunctionExpression`, and `FunctionDeclaration` boundaries — only detect top-level returns.

## Impact

Breaks `@sortBy` and `@groupBy` in `@mlld/array` registry module. Any exe using block-body arrow callbacks in js blocks is affected. Skipped tests reference closed issue #254 (which was about test isolation, not this bug) — unskip them after fixing.

