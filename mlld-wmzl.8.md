---
id: mlld-wmzl.8
status: closed
deps: [mlld-wmzl.4]
links: []
created: 2026-01-16T07:19:53.085229-08:00
type: task
priority: 1
parent: mlld-wmzl
---
# Phase 8: MCP server lifecycle orchestration

## Goal

Actually use @mcpConfig() return value to spawn MCP servers. Currently, the CLI calls @mcpConfig() but **discards the result**.

## Current State

**File**: \`cli/commands/env.ts\`

The spawn command calls @mcpConfig() but doesn't do anything with the result:
```typescript
// Current: Result is discarded!
const mcpConfigResult = await executeEnvExport(loaded, 'mcpConfig', []);
// ... result not used
```

## @mcpConfig() Return Format

```typescript
interface McpServerConfig {
  servers: Array<{
    module: string;      // e.g., "@github/issues"
    tools: string[] | '*';  // Tool filter
  }>;
}
```

## Implementation

### 1. Update spawnEnvCommand

**File**: \`cli/commands/env.ts\`

```typescript
// 1. Call @mcpConfig with profile context
env.setVariable('@mx.profile', selectedProfile);
const mcpConfigResult = await executeEnvExport(loaded, 'mcpConfig', []);

if (mcpConfigResult) {
  const config = mcpConfigResult as McpServerConfig;
  
  // 2. Spawn MCP servers
  const serverProcesses = [];
  for (const server of config.servers) {
    const toolsArg = Array.isArray(server.tools) 
      ? server.tools.join(',') 
      : '*';
    
    const proc = spawn('mlld', ['mcp', server.module, '--tools', toolsArg], {
      stdio: ['pipe', 'pipe', 'inherit']
    });
    serverProcesses.push(proc);
  }
  
  // 3. Track for cleanup
  env.setMcpServers(serverProcesses);
}
```

### 2. Create MCP Orchestrator

**File**: \`cli/mcp/MCPOrchestrator.ts\` (NEW)

```typescript
export class MCPOrchestrator {
  private servers: ChildProcess[] = [];
  
  async spawnServers(config: McpServerConfig): Promise<void> {
    for (const serverConfig of config.servers) {
      const proc = await this.spawnServer(serverConfig);
      this.servers.push(proc);
    }
  }
  
  private async spawnServer(config: { module: string; tools: string[] | '*' }): Promise<ChildProcess> {
    const args = ['mcp', config.module];
    if (config.tools !== '*') {
      args.push('--tools', config.tools.join(','));
    }
    
    return spawn('mlld', args, {
      stdio: ['pipe', 'pipe', 'inherit']
    });
  }
  
  async cleanup(): Promise<void> {
    for (const server of this.servers) {
      server.kill();
    }
    this.servers = [];
  }
}
```

### 3. Aggregate via Proxy (if multiple servers)

When multiple MCP servers are spawned, they need to be aggregated so the agent sees a single MCP endpoint.

```typescript
// If multiple servers, create aggregating proxy
if (serverProcesses.length > 1) {
  const proxy = new MCPProxy(serverProcesses);
  await proxy.start();
}
```

### 4. Inject Connection Info

The spawned agent process needs MCP connection info:

```typescript
// Inject MCP server connection into agent env
const agentEnv = {
  ...process.env,
  MCP_SERVER_URL: proxyUrl,  // Or direct server URL if single
};

// Spawn agent with MCP connection
spawn('claude', ['-p', prompt], { env: agentEnv });
```

## Related Issue

This overlaps with `mlld-ohya` (HIGH: MCP server lifecycle not implemented). Consider merging or marking one as duplicate.

## Exit Criteria

- [ ] @mcpConfig() result is parsed
- [ ] MCP servers spawned based on config
- [ ] Tool filtering respected
- [ ] Multiple servers aggregated via proxy
- [ ] Connection info injected into agent process
- [ ] Cleanup on exit
- [ ] Tests verify server lifecycle

## References

- Spec Section 6.4: MCP Server Lifecycle
- Plan Phase 8: `todo/plan-security-2026-v3.md` lines 673-720
- Related: `mlld-ohya`

## Estimated Effort
~6 hours


