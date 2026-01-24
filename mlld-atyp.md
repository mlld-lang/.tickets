---
id: mlld-atyp
status: closed
deps: [mlld-yb8a]
links: []
created: 2026-01-14T15:18:26.747166-08:00
type: task
priority: 0
parent: mlld-7suk
---
# Phase 3.1: Apply complete MCP security tracking [SECURITY-CRITICAL]

## Summary
Apply full security tracking (labels, taint, sources) to all MCP tool invocations, including zero-argument tools.

## Security Impact
**CRITICAL**: Without this, guards cannot detect LLM-influenced operations. Zero-arg tools currently bypass tracking entirely.

## Context
From audit-mcp.md: `FunctionRouter.ts:71-102` creates argument nodes without security descriptors. Zero-arg tools have no arguments to taint, so they bypass security tracking completely.

### Current Problem
```typescript
// FunctionRouter.ts:71-102
private createArgumentNodes(...): TextNode[] {
  nodes.push(this.createTextNode(String(args[paramName]), location));
  // NO security descriptor applied
}
```

A zero-arg MCP tool like `getTime()` has no arguments, so nothing gets tainted, and guards can't see it came from MCP.

## Implementation

### Part A: MCP Argument Security Descriptors

Location: `cli/mcp/FunctionRouter.ts:71-102`

For each argument, create security descriptor:
```typescript
const argDescriptor = makeSecurityDescriptor({
  labels: ["untrusted"],      // Semantic: LLM data is untrusted
  taint: ["src:mcp"],         // Provenance: came from MCP
  sources: [`mcp:${toolName}`] // Operation: which tool
});
```

Apply when creating TextNode:
```typescript
const textNode = this.createTextNode(String(args[paramName]), location);
textNode.security = argDescriptor;
// Or wrap in Variable with descriptor
```

### Part B: Invocation-Level Descriptor (Fixes Zero-Arg)

Location: `cli/mcp/FunctionRouter.ts:45-69`

Create descriptor that applies to entire invocation:
```typescript
const invocationDescriptor = makeSecurityDescriptor({
  labels: ["untrusted"],
  taint: ["src:mcp"],
  sources: [`mcp:${toolName}`]
});

// Attach to ExecInvocation node metadata
execNode.meta = {
  ...execNode.meta,
  security: invocationDescriptor
};
```

### Part C: Merge Into Guard Context

Location: `interpreter/hooks/guard-pre-hook.ts`

When building guard context, include invocation descriptor:
```typescript
// Get invocation descriptor from node.meta.security
const invocationSecurity = node.meta?.security;

// Merge into guard context
const guardContext = {
  labels: [...inputLabels, ...(invocationSecurity?.labels || [])],
  taint: [...inputTaint, ...(invocationSecurity?.taint || [])],
  sources: [...inputSources, ...(invocationSecurity?.sources || [])],
};
```

### Part D: Merge Into Result Descriptor

Location: `interpreter/eval/exec-invocation.ts`

Tool outputs should carry both:
- `src:exec` - execution happened
- `src:mcp` - originated from MCP invocation

```typescript
// When building result descriptor
const resultDescriptor = {
  labels: outputLabels,
  taint: [...outputTaint, "src:exec", ...(invocationSecurity?.taint || [])],
  sources: [...outputSources, ...(invocationSecurity?.sources || [])],
};
```

## Exit Criteria
- [ ] MCP tool arguments have `labels: ["untrusted"]`
- [ ] MCP tool arguments have `taint: ["src:mcp"]`
- [ ] MCP tool arguments have `sources: ["mcp:toolName"]`
- [ ] Zero-arg tools still show `@mx.taint.includes("src:mcp")` in guards
- [ ] Zero-arg tools still show `@mx.sources.includes("mcp:toolName")` in guards
- [ ] Tool outputs carry both `src:exec` and `src:mcp` taint
- [ ] All existing MCP tests pass
- [ ] Build succeeds

## Tests to Add

Create `tests/cases/security/mcp-taint/arg-tracking/`
(Test MCP args have proper taint - may need mock MCP server)

Create `tests/cases/security/mcp-taint/zero-arg-tracking/`
```
Test that zero-arg MCP tool:
- Guard sees @mx.taint.includes("src:mcp")
- Guard sees @mx.sources.includes("mcp:getTime")
- Guard can block based on MCP origin
```

Create `tests/cases/security/mcp-taint/output-tracking/`
```
Test that MCP tool outputs:
- Have taint: ["src:exec", "src:mcp"]
- Sources include mcp:toolName
```

## Dependencies
- Phase 1.2 (@mx.taint exposure) - guards need to see taint

## Estimated Effort
6 hours


