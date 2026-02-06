---
id: m-bec8
status: closed
deps: []
created: 2026-02-05T00:56:19Z
type: task
priority: 2
assignee: Adam
tags: [urgency-high, documentation-fix]
updated: 2026-02-05T01:03:34Z
---
# Fix invalid guard syntax in mcp-guards.md

## Problem

The mcp-guards.md atom contains invalid mlld syntax at lines 34-40:

```mlld
guard @auditMcp after op:exe = when [
  @mx.taint.includes("src:mcp") => [
    log `MCP call: @mx.op.name returned @output`
    allow
  ]
  * => allow
]
```

Guard when-handlers only support single actions (allow, deny, retry), not block syntax with multiple statements. This fails validation.

## Fix

Replace lines 31-43 with a valid after-guard example that demonstrates the concept without invalid syntax.

Option A: Simple allow with explanatory text
```mlld
guard @auditMcp after op:exe = when [
  @mx.taint.includes("src:mcp") => allow
  * => allow
]
```

With prose explaining: "After-guards can inspect MCP outputs via @output. For auditing, log statements should be placed outside the guard or in an exe function."

Option B: Remove the audit example entirely and just keep the blocking example.

## Location

/Users/adam/dev/mlld/docs/src/atoms/security/mcp-guards.md lines 31-43

## Verification

After fixing, run: `mlld validate tmp/mcp-guards-test.mld` where the test file contains just the code blocks from the atom.


**2026-02-05 01:03 UTC:** The invalid `=> [ log ... allow ]` syntax was replaced with a valid `@output.error => deny` pattern. The atom now explains that complex audit logic should use wrapper exe functions instead of guards.
