---
id: m-66be
status: closed
deps: []
created: 2026-01-30T05:19:41Z
type: bug
priority: 2
assignee: Adam
updated: 2026-01-30T05:48:50Z
---
# Ternary expression in template shows AST node instead of resolved value

When a ternary expression is interpolated in a template or show statement, it sometimes outputs the raw AST node instead of the resolved value.

**Observed in:**
```
show `Verify: @verifyResult.status`
```

**Output:**
```
Verify: {"type":"TernaryExpression","nodeId":"...", ...}
```

**Expected:** The resolved string value (e.g., "pass" or "fail")

**Context:** This occurred in a script where `@verifyResult` was built with:
```mlld
=> { status: @testResult.passed ? "pass" : "fail", output: @testResult.output }
```

The ternary wasn't being evaluated before being stored in the object.

