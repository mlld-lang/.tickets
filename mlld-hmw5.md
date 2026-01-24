---
id: mlld-hmw5
status: closed
deps: [mlld-7si8, mlld-19ya, mlld-7ud1]
links: []
created: 2026-01-14T15:21:07.78304-08:00
type: task
priority: 1
parent: mlld-x60s
---
# Phase 6.3: mlld env spawn command

## Summary
Implement `mlld env spawn <name> -- <command>` to run a command with an environment's credentials and config.

## Context
This is the core command for using environment modules. It loads the environment, retrieves credentials, matches /wants against policy, and spawns the agent process.

## Implementation

### Command Structure

```bash
mlld env spawn <name> -- <command> [args...]
mlld env spawn claude-dev -- claude -p "Fix the bug"
```

### Implementation

Location: `cli/commands/env/spawn.ts`

```typescript
export async function spawnEnvCommand(args: string[]): Promise<void> {
  // Parse args: everything before -- is our args, after is the command
  const dashIndex = args.indexOf('--');
  if (dashIndex === -1) {
    console.error('Usage: mlld env spawn <name> -- <command>');
    process.exit(1);
  }
  
  const envName = args[0];
  const command = args.slice(dashIndex + 1);
  
  if (!envName || command.length === 0) {
    console.error('Usage: mlld env spawn <name> -- <command>');
    process.exit(1);
  }
  
  // Load environment module
  const envDir = await findEnvModule(envName);
  if (!envDir) {
    console.error(`Environment not found: ${envName}`);
    process.exit(1);
  }
  
  // Create interpreter and load module
  const env = createEnvironment({
    cwd: envDir,
    // ... other options
  });
  
  const moduleResult = await loadAndEvaluate(
    path.join(envDir, 'index.mld'),
    env
  );
  
  // Match /wants against policy → set tier
  const tier = selectWantsTier(
    env.getModuleWants(),
    env.getPolicyConfig()
  );
  env.setContextValue('mx.policy.tier', tier);
  
  // Get @spawn export
  const spawnFn = moduleResult.exports?.get('@spawn');
  if (!spawnFn) {
    // Fallback: inject env vars and run command directly
    const token = await getKeychainProvider().get('mlld-env', envName);
    
    const proc = spawn(command[0], command.slice(1), {
      env: {
        ...process.env,
        CLAUDE_CODE_OAUTH_TOKEN: token,
        CLAUDE_CONFIG_DIR: path.join(envDir, '.claude'),
      },
      stdio: 'inherit'
    });
    
    proc.on('exit', (code) => process.exit(code || 0));
    return;
  }
  
  // Call @spawn with prompt (rest of command joined)
  const prompt = command.slice(1).join(' ');
  await callFunction(spawnFn, [prompt], env);
}
```

### Spawn Lifecycle

1. Load environment module
2. Check /needs against system capabilities  
3. Match /wants against policy → determine tier
4. Set @mx.policy.tier in context
5. Call @mcpConfig() if exists (for Phase 7)
6. Spawn MCP servers if configured (for Phase 7)
7. Retrieve credentials from keychain
8. Call @spawn(prompt) or run command with env vars
9. Wait for process exit
10. Cleanup (MCP servers, temp files)

### Error Handling

- Missing environment: clear error message
- Missing credentials: prompt to run capture or set manually
- /needs unmet: error from Phase 2.3
- /wants tier unavailable: run with most permissive allowed tier

## Exit Criteria
- [ ] `mlld env spawn name -- command` works
- [ ] Credentials loaded from keychain
- [ ] Environment variables injected into spawned process
- [ ] /wants tier selection works (from Phase 2.1)
- [ ] @spawn export called if available
- [ ] Process exit code propagated
- [ ] Error handling for missing env/creds

## Tests to Add
- Test command parsing (-- separator)
- Test environment loading
- Test credential injection
- Test fallback when no @spawn export
- Integration test with actual Claude (manual)

## Dependencies
- Phase 1.1 (rename env → vars)
- Phase 2.1 (tier selection respects policy)
- Phase 4.2 (keychain implementation)
- Phase 5.1 (environment module type)
- Phase 6.1 (env list - for finding modules)

## Estimated Effort
4 hours


