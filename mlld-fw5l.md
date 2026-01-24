---
id: mlld-fw5l
status: closed
deps: []
links: []
created: 2026-01-14T20:03:30.238105-08:00
type: bug
priority: 2
parent: mlld-2ubc
---
# B4: Validate module type in findEnvModule [MEDIUM]

## Problem

`findEnvModule()` only checks if `module.yml` exists, not that it has `type: environment`. Non-environment modules are incorrectly treated as environments.

## Current Code (cli/commands/env.ts:19)

```typescript
async function findEnvModule(name: string): Promise<string | null> {
  const localPath = path.join(process.cwd(), '.mlld/env', name);
  const globalPath = path.join(os.homedir(), '.mlld/env', name);
  
  // Only checks existence!
  if (await exists(path.join(localPath, 'module.yml'))) {
    return localPath;
  }
  if (await exists(path.join(globalPath, 'module.yml'))) {
    return globalPath;
  }
  return null;
}
```

## Required Fix

```typescript
async function findEnvModule(name: string): Promise<string | null> {
  const localPath = path.join(process.cwd(), '.mlld/env', name);
  const globalPath = path.join(os.homedir(), '.mlld/env', name);
  
  for (const envPath of [localPath, globalPath]) {
    const manifestPath = path.join(envPath, 'module.yml');
    if (await exists(manifestPath)) {
      const manifest = await loadManifest(manifestPath);
      if (manifest.type === 'environment') {
        return envPath;
      }
      // Log warning: found module but wrong type
      console.warn(`Module at ${envPath} is type '${manifest.type}', not 'environment'`);
    }
  }
  return null;
}
```

## Files to Modify

1. `cli/commands/env.ts:19`
   - Enhance findEnvModule to validate type
   - Load and parse module.yml
   - Check `type: environment`

## Exit Criteria

- [ ] Only `type: environment` modules returned by findEnvModule
- [ ] Other module types produce warning
- [ ] Clear error when environment not found vs wrong type

## Estimated Effort
1 hour


