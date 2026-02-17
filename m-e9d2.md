---
id: m-e9d2
status: closed
deps: []
created: 2026-02-16T17:07:41Z
type: bug
priority: 0
assignee: Adam
tags: [bug, validate, p0, qa-driven]
updated: 2026-02-16T17:21:27Z
---
# bug: mlld validate misses undefined variables in exe invocation arguments

## Problem

`mlld validate` does not detect undefined variable references when they appear as arguments to exe invocations. The `walkAST` function skips these positions.

## Repro

```mlld
exe @greet(name) = show `Hello @name`
var @result = @greet(@typo)
```

Expected: warning about `@typo` being undefined.
Actual: no warning.

## Where to look

- The undefined variable detection logic — search for `undefinedVariable` or `walkAST` in `cli/`
- The AST walker needs to traverse into function call argument nodes
- Compare with how it handles other expression positions (var assignments, template interpolations) where it does detect undefined vars

## Fix

Extend `walkAST` to visit variable references inside exe invocation argument lists. The AST node for a function call should have an `args` or `arguments` array — walk each one.

## Verification

```bash
echo 'exe @greet(name) = show `Hello @name`
var @result = @greet(@typo)' > tmp/validate-test.mld
mlld validate tmp/validate-test.mld
```

Should warn about `@typo` being undefined.

## Source
QA run: qa/runs/2026-02-16-0/topics/validate-features/


**2026-02-16 17:21 UTC:** Extended analyze AST traversal to visit ExecInvocation commandRef arguments, added regression asserting undefined variable warnings for @greet(@typo), and verified via npm test.
