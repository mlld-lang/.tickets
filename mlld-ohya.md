---
id: mlld-ohya
status: open
deps: [mlld-8o14]
links: []
created: 2026-01-15T11:04:14.929329-08:00
type: bug
priority: 1
parent: mlld-urik
---
# HIGH: MCP server lifecycle not implemented

## Problem

`mlld env spawn` executes @mcpConfig but discards the result. MCP servers are never spawned.

## Current State

From gpt52 at `cli/commands/env.ts:577`:
- @mcpConfig() is called (if exported)
- Result is captured but never used
- No MCP server spawning
- No tool filter application

## Expected Flow (from spec)

```
mlld env spawn my-env -- claude -p "task"
  1. Load environment module
  2. Resolve /wants tier
  3. Call @mcpConfig() ✅ (done)
  4. Parse MCP server configs
  5. Spawn MCP server processes  ❌ (missing)
  6. Apply tool filters          ❌ (missing)
  7. Execute @spawn with MCP servers available
  8. Cleanup on exit             ❌ (missing)
```

## Required Implementation

### 1. MCP Server Manager

```typescript
// cli/env/McpServerManager.ts
export class McpServerManager {
  private servers: Map<string, ChildProcess> = new Map();
  
  async spawnServer(config: McpServerConfig): Promise<void> {
    const proc = spawn('mlld', ['mcp', config.module], {
      env: { ...process.env, ...config.env },
      stdio: ['pipe', 'pipe', 'inherit']
    });
    
    await this.waitForReady(proc);
    this.servers.set(config.module, proc);
  }
  
  async cleanup(): Promise<void> {
    for (const proc of this.servers.values()) {
      proc.kill('SIGTERM');
    }
  }
}
```

### 2. Integration in spawn command

```typescript
// After calling @mcpConfig()
if (mcpConfig?.servers?.length > 0) {
  const manager = new McpServerManager();
  
  for (const server of mcpConfig.servers) {
    await manager.spawnServer(server);
  }
  
  // Cleanup on exit
  process.on('exit', () => manager.cleanup());
  process.on('SIGINT', () => manager.cleanup());
}
```

## Exit Criteria

- [ ] @mcpConfig() result parsed for server configs
- [ ] MCP servers spawned as child processes
- [ ] Tool filters applied (if specified)
- [ ] Servers cleaned up on process exit
- [ ] Test with mock MCP server

## Estimated Effort
6-8 hours


