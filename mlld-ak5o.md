---
id: mlld-ak5o
status: closed
deps: [mlld-abpb, mlld-tiha, mlld-94a7]
links: []
created: 2026-01-14T20:02:47.274972-08:00
type: bug
priority: 1
parent: mlld-2ubc
---
# B1: env spawn/shell must evaluate environment module [HIGH]

## Problem

`mlld env spawn` and `mlld env shell` bypass the environment module contract. They:
- Don't load/evaluate the module
- Don't resolve /wants tier against policy
- Don't call @mcpConfig()
- Don't invoke exported @spawn/@shell functions
- Just inject env vars and spawn process directly

## Current Broken Flow (cli/commands/env.ts:253-307)

```typescript
async function spawnEnvCommand(args) {
  // Find module directory
  const envDir = await findEnvModule(envName);
  
  // Get token from keychain directly
  const token = await keychainProvider.get('mlld-env', envName);
  
  // Spawn process with env vars - BYPASSES MODULE!
  spawn(command[0], command.slice(1), {
    env: {
      CLAUDE_CODE_OAUTH_TOKEN: token,
      CLAUDE_CONFIG_DIR: path.join(envDir, '.claude'),
    }
  });
}
```

## Required Flow (per spec)

```typescript
async function spawnEnvCommand(args) {
  const envDir = await findEnvModule(envName);
  
  // 1. Create interpreter environment
  const env = createEnvironment({ cwd: envDir });
  
  // 2. Load and evaluate module
  const moduleResult = await loadAndEvaluate(
    path.join(envDir, 'index.mld'),
    env
  );
  
  // 3. /needs validation happens during load (fail fast)
  
  // 4. Match /wants against policy
  const tier = selectWantsTier(
    env.getModuleWants(),
    env.getPolicyConfig()
  );
  env.setTier(tier);
  
  // 5. Call @mcpConfig() if exported
  const mcpConfigFn = moduleResult.exports?.get('@mcpConfig');
  if (mcpConfigFn) {
    const mcpConfig = await callFunction(mcpConfigFn, [], env);
    // Spawn MCP servers per config...
  }
  
  // 6. Call exported @spawn(prompt) or @shell()
  const spawnFn = moduleResult.exports?.get('@spawn');
  await callFunction(spawnFn, [prompt], env);
}
```

## Files to Modify

1. `cli/commands/env.ts:253-307` (spawn command)
   - Complete rewrite to evaluate module
   
2. `cli/commands/env.ts:309-353` (shell command)
   - Similar rewrite

3. May need new utilities:
   - `cli/env/ModuleEvaluator.ts` - Load and evaluate env module
   - `cli/env/McpServerManager.ts` - Spawn MCP servers (if not done in Phase 7)

## Exit Criteria

- [ ] Module's index.mld is evaluated, not just located
- [ ] /needs are validated at load time
- [ ] /wants tier resolved against active policy
- [ ] @mcpConfig() called if exported (MCP servers spawned)
- [ ] @spawn(prompt) or @shell() exports invoked
- [ ] Guards active during module execution
- [ ] Tests verify full contract

## Estimated Effort
8-12 hours (significant rework)


