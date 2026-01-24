---
id: mlld-u8k1
status: closed
deps: []
links: []
created: 2026-01-14T20:04:57.904761-08:00
type: bug
priority: 2
parent: mlld-j5bw
---
# C3: Preserve structured MCP arguments

## Problem

MCP tool arguments are stringified with `String(...)`, so objects and arrays become `"[object Object]"` instead of preserving structure.

## Current Code (FunctionRouter.ts:107)

```typescript
private createArgumentNodes(...): TextNode[] {
  // ...
  nodes.push(this.createTextNode(String(args[paramName]), location));
  //                             ^^^^^^^ Objects become "[object Object]"
}
```

## Impact

```json
// MCP call
{ "tool": "myTool", "args": { "config": { "key": "value" } } }
```

```mlld
// In mlld, @config becomes the string "[object Object]"
// instead of a structured object
```

## Required Fix

Preserve structured values by creating appropriate node types:

```typescript
private createArgumentNodes(...): (TextNode | StructuredValueNode)[] {
  const nodes = [];
  
  for (const paramName of paramNames) {
    const value = args[paramName];
    
    if (typeof value === 'object' && value !== null) {
      // Create structured value node
      nodes.push(this.createStructuredValueNode(value, location));
    } else {
      // Primitive - use text node
      nodes.push(this.createTextNode(String(value), location));
    }
  }
  
  return nodes;
}

private createStructuredValueNode(value: object, location): StructuredValueNode {
  return {
    type: 'StructuredValue',
    nodeId: randomUUID(),
    location,
    value: value,  // Preserve structure
    // Also need security descriptor for MCP tracking
    meta: {
      mcpSecurity: this.createMcpSecurityDescriptor(toolName)
    }
  };
}
```

## Files to Modify

1. `cli/mcp/FunctionRouter.ts:107`
   - Change createArgumentNodes to preserve structure
   
2. May need to check how structured values flow through:
   - `interpreter/eval/exec-invocation.ts`
   - Parameter handling

## Exit Criteria

- [ ] Object arguments remain objects, not "[object Object]"
- [ ] Array arguments remain arrays
- [ ] Primitive arguments still work
- [ ] Structured values carry MCP security descriptor
- [ ] Test with complex nested objects

## Estimated Effort
3-4 hours


