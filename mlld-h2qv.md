---
id: mlld-h2qv
status: open
deps: [mlld-hmw5]
links: []
created: 2026-01-14T15:22:28.09994-08:00
type: task
priority: 2
---
# Phase 7.1: MCP server manager

## Summary
Create McpServerManager to spawn and manage MCP servers from @mcpConfig().

## Context
Environment modules can define @mcpConfig() to configure MCP tools for the agent. The server manager handles spawning and lifecycle.

## Implementation

### Location: `cli/env/McpServerManager.ts`

```typescript
export class McpServerManager {
  private servers: Map<string, McpServer> = new Map();
  
  async spawnServer(config: McpServerConfig): Promise<McpServer> {
    // Spawn mlld mcp <module> subprocess
    const proc = spawn('mlld', ['mcp', config.module], {
      env: {
        ...process.env,
        ...config.env,
      },
      stdio: ['pipe', 'pipe', 'inherit']
    });
    
    const server = new McpServer(proc, config);
    await server.waitForReady();
    
    this.servers.set(config.module, server);
    return server;
  }
  
  async cleanup(): Promise<void> {
    for (const server of this.servers.values()) {
      await server.shutdown();
    }
    this.servers.clear();
  }
}

class McpServer {
  async waitForReady(): Promise<void> {
    // Wait for JSON-RPC initialization
  }
  
  async shutdown(): Promise<void> {
    this.proc.kill('SIGTERM');
    // Wait with timeout, then SIGKILL if needed
  }
}
```

### Integration with spawn

In Phase 6.3 spawn, after loading environment:
```typescript
const mcpConfig = await callFunction(moduleResult.exports.get('@mcpConfig'), [], env);
if (mcpConfig?.servers?.length > 0) {
  const manager = new McpServerManager();
  for (const server of mcpConfig.servers) {
    await manager.spawnServer(server);
  }
  // Cleanup on exit
  process.on('exit', () => manager.cleanup());
}
```

## Exit Criteria
- [ ] Can spawn MCP server subprocess
- [ ] Server ready detection works
- [ ] Graceful shutdown on process exit
- [ ] Cleanup handles multiple servers

## Dependencies
- Phase 6.3 (spawn command)

## Estimated Effort
4 hours


