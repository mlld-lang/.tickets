---
id: mlld-964m
status: open
deps: [mlld-69qu]
links: []
created: 2026-01-22T16:34:03.014888-08:00
type: task
priority: 2
parent: mlld-4z2w
---
# Phase 3a: External MCP server spawning

## Goal

Extend MCPOrchestrator to spawn arbitrary MCP servers (not just mlld modules).

## Context

Currently Orchestrator only spawns `mlld mcp <module>`:
```typescript
// cli/mcp/MCPOrchestrator.ts:314
spawn('mlld', ['mcp', server.module], {...})
```

Many MCP servers are standalone:
- `npx @anthropic/mcp-server-filesystem /path`
- `python -m my_mcp_server`
- `/usr/local/bin/some-mcp-server`

## Syntax

```mlld
var tools @externalTools = {
  servers: [
    >> mlld module (existing)
    { module: "@company/tools" },
    
    >> Arbitrary command
    { 
      command: "npx",
      args: ["@anthropic/mcp-server-filesystem", "/workspace"]
    },
    
    >> Shorthand for npm packages
    { npm: "@anthropic/mcp-server-filesystem", args: ["/workspace"] }
  ]
}
```

## Integration Points

1. **McpServerConfig**: `cli/mcp/MCPOrchestrator.ts` - Add command/args fields
2. **Spawn Logic**: Handle command vs module
3. **npm Shorthand**: `npm: "pkg"` → `npx pkg`
4. **Tool Routing**: Route calls through mlld guards before forwarding

## Critical: Guard Integration

External MCP calls MUST flow through mlld guards:
```
MCP call → FunctionRouter → Guards → Forward to external server
```

NOT:
```
MCP call → Direct forward (WRONG - bypasses guards)
```

## Exit Criteria

- [ ] `command` + `args` spawns arbitrary servers
- [ ] `npm` shorthand works
- [ ] External tool calls route through guards
- [ ] `src:mcp` taint applied to external results
- [ ] Test: external MCP server integration

## Estimated Effort
~6 hours

## Parent Epic
mlld-4z2w

## Depends On
mlld-69qu (tool labels - guards need to work first)


