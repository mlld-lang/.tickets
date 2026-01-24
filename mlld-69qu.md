---
id: mlld-69qu
status: open
deps: [mlld-9uju]
links: []
created: 2026-01-22T16:33:42.466286-08:00
type: task
priority: 1
parent: mlld-4z2w
---
# Phase 2d: Tool labels in guard context

## Goal

Make tool-level labels available to guards so policy can control destructive/net:w/etc operations.

## Context

Tools in a collection can have labels:
```mlld
var tools @agentTools = {
  deleteRepo: {
    mlld: @deleteRepository,
    labels: [destructive, net:w]
  }
}
```

Guards need to see these labels in `@mx.op.labels` to make decisions.

## Current State

FunctionRouter already creates mcpSecurityDescriptor with `src:mcp` taint:
```typescript
// cli/mcp/FunctionRouter.ts:78-82
private createMcpSecurityDescriptor(toolName: string): SecurityDescriptor {
  return makeSecurityDescriptor({
    taint: ['src:mcp'],
    sources: [`mcp:${toolName}`]
  });
}
```

## Enhancement

Add tool labels to operation context:
```typescript
// When tool has labels: [destructive, net:w]
operationContext = {
  type: 'exe',
  name: 'deleteRepo',
  labels: ['destructive', 'net:w'],  // From tool definition
  source: 'mcp:deleteRepo'  // Origin tracking
}
```

## Integration Points

1. **FunctionRouter**: Look up tool definition, extract labels
2. **Operation Context**: `@mx.op.labels` includes tool labels
3. **Guard Hooks**: `interpreter/hooks/guard-pre-hook.ts` - labels available
4. **Policy**: Labels flow to policy label rules

## Guard Usage

```mlld
>> Block destructive tools from untrusted input
guard @noUntrustedDestructive before op:exe = when [
  @mx.taint.includes("src:mcp") && @mx.op.labels.includes("destructive")
    => deny "Untrusted data cannot trigger destructive operations"
  * => allow
]

>> Or via policy rules
policy @config = {
  labels: {
    "src:mcp": { deny: [destructive] }
  }
}
```

## Exit Criteria

- [ ] Tool labels extracted from ToolDefinition
- [ ] Labels added to operation context
- [ ] `@mx.op.labels` shows tool labels in guards
- [ ] Policy label rules work with tool labels
- [ ] Test: guard blocks based on tool label

## Estimated Effort
~3 hours

## Parent Epic
mlld-4z2w

## Depends On
mlld-9uju (tool collection syntax)


