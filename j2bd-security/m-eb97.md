---
id: m-eb97
status: closed
deps: []
created: 2026-02-04T05:37:58Z
type: task
priority: 2
assignee: Adam
tags: [urgency-high, j2bd-mcp]
updated: 2026-02-04T05:42:16Z
---
# Fix demo Step 3 guard pattern and summary block

## Problem

EC3 fails because the demo's guard uses `for op:exe` filter which has empty @input for user-defined exe calls. The `for op:exe` guard pattern is architecturally unsuited for checking input data labels.

## Verified Working Pattern

Tested in tmp/ec3-fix-test2.mld and tmp/ec3-fix-test3.mld:

```mlld
guard @blockSecretExe for secret = when [
  @mx.op.type == "exe" => deny "Secret data cannot flow to executable operations"
  * => allow
]
```

This:
- Allows `var secret @apiKey = ...` (op type is 'var')
- Blocks `@tryEchoSecret(@apiKey)` (op type is 'exe', input has secret)
- Blocks `@tryEchoObj({ text: @apiKey })` (object carries secret from fix 5f04b9ea0)
- Works with denied handler in exe when blocks

## Changes Required

### 1. pipeline/mcp-security-demo.mld Step 3 (lines 46-49)

Replace:
```mlld
guard @noSecretToMcp for op:exe = when [
  @mx.taint.includes("src:mcp") && @input.any.mx.labels.includes("secret") => deny "Secrets cannot flow to MCP tools"
  * => allow
]
```

With:
```mlld
guard @noSecretToMcp for secret = when [
  @mx.op.type == "exe" => deny "Secret data cannot flow to executable operations"
  * => allow
]
```

Update the surrounding comments (lines 43-45) to explain: data-label guard fires when secret data flows through any operation, condition narrows to exe calls only.

### 2. pipeline/mcp-security-demo.mld Step 5 summary (lines 107-138)

The show :: ... :: block at line 107-138 crashes with FieldAccess error because the old `for op:exe` guard was firing on show operations. With the new `for secret` guard pattern, this crash should be fixed since show doesn't carry secret input. But verify - if the show block interpolates the @blockedResult which has secret taint, the guard may fire. If so, the summary should avoid interpolating secret-tainted values, or use plain text.

Also update the summary text in Step 3 section (lines 122-125) to reflect the actual guard pattern used.

### 3. docs/atoms/security/mcp-guards.md

Update the guard example to use `for secret` pattern instead of `for op:exe`. The atom should show the correct working pattern.

## Acceptance Criteria

1. `mlld pipeline/mcp-security-demo.mld` runs without errors
2. Step 3 output shows BLOCKED (not the raw secret key)
3. Step 5 summary show block renders without FieldAccess error
4. Safe MCP calls (no secrets) still work
5. mcp-guards atom example uses correct pattern

