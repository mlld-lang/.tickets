---
id: mlld-794g
status: closed
deps: [mlld-9uju]
links: []
created: 2026-01-23T07:51:01.456498-08:00
type: task
priority: 1
parent: mlld-4z2w
---
# Phase 2b-1: Tool filtering in MCPServer

## Goal

Extend MCPServer to accept a tool collection reference and filter tools/list responses accordingly.

## Context

This is the **simpler half** of environment tool scoping. Instead of building full `env` directive infrastructure, we extend the existing tool filtering pattern.

**Current pattern** (mcp.ts:156):
```typescript
for (const [name] of Array.from(exportedFunctions.entries())) {
  if (!toolNames.has(name)) {
    exportedFunctions.delete(name);
  }
}
```

## Proposed Enhancement

Accept tool collection at MCPServer initialization:

```typescript
interface MCPServerOptions {
  environment: Environment;
  exportedFunctions: Map<string, ExecutableVariable>;
  toolCollection?: ToolCollection;  // NEW: from var tools @x = {...}
}
```

When `toolCollection` is provided:
1. Filter `exportedFunctions` to only tools in collection
2. Apply tool-specific labels from collection
3. Apply bind/expose reshaping (if 6x71 complete)

## Integration Points

1. **MCPServer constructor**: Accept toolCollection option
2. **tools/list handler**: Return only tools in collection
3. **tools/call handler**: Validate tool in collection before routing
4. **CLI**: Add `--tools-collection @varName` flag

## Attenuation Invariant

Tool collection can only **restrict**, never extend:
- Collection tools must be subset of exportedFunctions
- Error if collection references unknown tool

## Exit Criteria

- [ ] MCPServer accepts toolCollection option
- [ ] tools/list filtered to collection
- [ ] tools/call rejects tools outside collection
- [ ] CLI flag to specify collection
- [ ] Test: collection restricts available tools

## Estimated Effort
~4 hours

## Parent Epic
mlld-4z2w

## Depends On
mlld-9uju (tool collection syntax)


