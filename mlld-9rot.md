---
id: mlld-9rot
status: closed
deps: [mlld-7si8, mlld-hmw5]
links: []
created: 2026-01-14T15:21:21.610592-08:00
type: task
priority: 1
parent: mlld-x60s
---
# Phase 6.4: mlld env shell command

## Summary
Implement `mlld env shell <name>` to start an interactive agent session.

## Context
Shell is a convenience command that calls the environment's @shell() export for interactive use.

## Implementation

### Command Structure

```bash
mlld env shell <name>
```

### Implementation

Location: `cli/commands/env/shell.ts`

```typescript
export async function shellEnvCommand(args: string[]): Promise<void> {
  const envName = args[0];
  if (!envName) {
    console.error('Usage: mlld env shell <name>');
    process.exit(1);
  }
  
  // Load environment module
  const envDir = await findEnvModule(envName);
  if (!envDir) {
    console.error(`Environment not found: ${envName}`);
    process.exit(1);
  }
  
  const env = createEnvironment({ cwd: envDir });
  const moduleResult = await loadAndEvaluate(
    path.join(envDir, 'index.mld'),
    env
  );
  
  // Get @shell export
  const shellFn = moduleResult.exports?.get('@shell');
  if (!shellFn) {
    // Fallback: spawn interactive process
    const token = await getKeychainProvider().get('mlld-env', envName);
    
    const proc = spawn('claude', [], {
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
  
  // Call @shell()
  await callFunction(shellFn, [], env);
}
```

## Exit Criteria
- [ ] `mlld env shell name` starts interactive session
- [ ] Uses environment's @shell() export if available
- [ ] Falls back to spawning claude directly with env vars
- [ ] Credentials loaded from keychain
- [ ] Config directory set correctly
- [ ] Interactive session works (stdin/stdout/stderr inherited)

## Tests to Add
- Test command invocation
- Test @shell() export calling
- Test fallback behavior
- Manual test with actual Claude

## Dependencies
- Phase 6.3 (spawn command - shares code)

## Estimated Effort
2 hours


