---
id: mlld-abpb
status: closed
deps: []
links: []
created: 2026-01-14T20:01:31.613241-08:00
type: bug
priority: 0
parent: mlld-4fsa
---
# A1: Fix MCP taint timing - guards must see src:mcp [CRITICAL]

## Problem

Guards run BEFORE `meta.mcpSecurity` is merged into the security context. Result: `@mx.taint` and `@mx.sources` are **empty** for MCP tool calls.

## Current Broken Flow

```
1. FunctionRouter.buildInvocation() 
   → Creates mcpSecurity descriptor ✓
   → Attaches to node.meta.mcpSecurity ✓

2. evaluateExecInvocation()
   → Line 1835: runPreHooks() called  ← GUARDS RUN HERE
   → Line 1878: Parameters evaluated
   → Line 1948: mcpSecurity merged into result  ← TOO LATE!
```

Guards see:
```mlld
@mx.taint = []        // Empty!
@mx.sources = []      // Empty!
```

## Required Fix

Inject mcpSecurity into guard context BEFORE runPreHooks():

```typescript
// Around line 1830, BEFORE runPreHooks()
const mcpSecurity = (node as any).meta?.mcpSecurity;
if (mcpSecurity) {
  // Seed the guard context with MCP security info
  env.seedSecurityContext(mcpSecurity);
}

// Then run pre-hooks
const preResult = await runPreHooks(...);
```

Or alternatively, pass mcpSecurity to runPreHooks() and merge it into the guard snapshot.

## Files to Modify

1. `interpreter/eval/exec-invocation.ts`
   - Line ~1830: Inject mcpSecurity before runPreHooks()
   - May need new method on Environment to seed security context

2. `interpreter/hooks/guard-pre-hook.ts`
   - May need to accept mcpSecurity and merge into guardContext
   - Lines 866-892: Where guardContext is built

## Test Case

```mlld
/guard @blockMcp before op:exe = when [
  @mx.taint.includes("src:mcp") => deny "MCP blocked"
  * => allow
]

// When called via MCP, this guard should fire
/exe @myTool(arg) = show @arg
```

## Exit Criteria

- [ ] `@mx.taint.includes("src:mcp")` returns true in guards for MCP calls
- [ ] `@mx.sources.includes("mcp:toolName")` returns true in guards
- [ ] Zero-arg MCP tools also show taint (invocation-level descriptor)
- [ ] Test in `tests/cases/security/mcp-guard-taint/` passes

## Estimated Effort
4-6 hours


