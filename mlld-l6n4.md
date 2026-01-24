---
id: mlld-l6n4
status: closed
deps: [mlld-ut9i, mlld-bniq, mlld-7si8]
links: []
created: 2026-01-14T15:20:41.841357-08:00
type: task
priority: 1
parent: mlld-x60s
---
# Phase 6.2: mlld env capture command

## Summary
Implement `mlld env capture <name>` to create an environment module from current Claude config.

## Context
Users have existing Claude configurations in `~/.claude/`. This command captures that config into a reusable, shareable environment module.

## Implementation

### Command Structure

```bash
mlld env capture <name> [--global]
```

### What Gets Captured

From `~/.claude/`:
1. `.credentials.json` → token extracted, stored in keychain
2. `settings.json` → copied to `.claude/settings.json`
3. `CLAUDE.md` → copied to `.claude/CLAUDE.md`  
4. `hooks.json` → copied to `.claude/hooks.json`

NOT copied:
- `.credentials.json` (token goes to keychain, file not copied)
- Session files, logs, temporary data

### Implementation

Location: `cli/commands/env/capture.ts`

```typescript
export async function captureEnvCommand(args: string[]): Promise<void> {
  const name = args[0];
  if (!name) {
    console.error('Usage: mlld env capture <name>');
    process.exit(1);
  }
  
  const isGlobal = args.includes('--global');
  const claudeDir = path.join(os.homedir(), '.claude');
  const targetDir = isGlobal 
    ? path.join(os.homedir(), '.mlld/env', name)
    : path.join(process.cwd(), '.mlld/env', name);
  
  // Check source exists
  if (!await exists(claudeDir)) {
    console.error('Claude config not found at ~/.claude/');
    process.exit(1);
  }
  
  // Create target directory
  await fs.mkdir(path.join(targetDir, '.claude'), { recursive: true });
  
  // Extract and store token
  const credsPath = path.join(claudeDir, '.credentials.json');
  if (await exists(credsPath)) {
    const creds = JSON.parse(await fs.readFile(credsPath, 'utf-8'));
    const token = creds.oauth_token || creds.token;
    if (token) {
      await getKeychainProvider().set('mlld-env', name, token);
      console.log('✓ Token stored in keychain');
    }
  }
  
  // Copy config files
  const filesToCopy = ['settings.json', 'CLAUDE.md', 'hooks.json'];
  for (const file of filesToCopy) {
    const src = path.join(claudeDir, file);
    const dest = path.join(targetDir, '.claude', file);
    if (await exists(src)) {
      await fs.copyFile(src, dest);
      console.log(`✓ Copied ${file}`);
    }
  }
  
  // Generate module.yml
  await fs.writeFile(path.join(targetDir, 'module.yml'), `
name: ${name}
type: environment
about: "Environment captured from ~/.claude"
version: 1.0.0
entry: index.mld
`.trim());
  
  // Generate index.mld template
  await fs.writeFile(path.join(targetDir, 'index.mld'), `
/needs { keychain, cmd: [claude] }

/var secret @token = keychain.get("mlld-env", "${name}")

/exe @spawn(prompt) = {
  CLAUDE_CODE_OAUTH_TOKEN=@token
  CLAUDE_CONFIG_DIR=@fm.dir/.claude
  claude -p @prompt
}

/exe @shell() = {
  CLAUDE_CODE_OAUTH_TOKEN=@token
  CLAUDE_CONFIG_DIR=@fm.dir/.claude
  claude
}

/export { @spawn, @shell }
`.trim());
  
  console.log(`\n✓ Created environment: ${targetDir}`);
  console.log(`  Use: mlld env spawn ${name} -- claude -p "..."`);
}
```

## Exit Criteria
- [ ] `mlld env capture myenv` creates `.mlld/env/myenv/`
- [ ] Token extracted from .credentials.json and stored in keychain
- [ ] settings.json, CLAUDE.md, hooks.json copied (if exist)
- [ ] .credentials.json NOT copied (security)
- [ ] module.yml generated with correct type
- [ ] index.mld template generated with spawn/shell functions
- [ ] Works with `--global` flag

## Tests to Add
- Test with mock Claude config directory
- Test token extraction from various credential formats
- Test file copying and template generation
- Test keychain storage (may need mock)

## Dependencies
- Phase 1.1 (rename env → vars)
- Phase 4.2 (keychain implementation)
- Phase 5.1 (environment module type)

## Estimated Effort
4 hours


