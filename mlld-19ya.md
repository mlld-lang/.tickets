---
id: mlld-19ya
status: closed
deps: [mlld-bniq, mlld-7si8]
links: []
created: 2026-01-14T15:20:16.000827-08:00
type: task
priority: 1
parent: mlld-x60s
---
# Phase 6.1: mlld env list command

## Summary
Implement `mlld env list` to show available environment modules.

## Context
Users need to see what environment modules are installed locally and globally.

## Implementation

### Command Structure

```bash
mlld env list [--json]
```

### Output Format

```
Available environments:

Local (.mlld/env/):
  claude-dev      Development Claude with full permissions
  claude-readonly Code review only Claude

Global (~/.mlld/env/):
  claude-prod     Production Claude configuration
  
(3 environments total)
```

### Implementation

Location: `cli/commands/env/list.ts`

```typescript
import { loadModuleManifest } from '@core/registry/ModuleLoader';
import path from 'path';
import fs from 'fs/promises';

export async function listEnvCommand(args: string[]): Promise<void> {
  const isJson = args.includes('--json');
  
  const localPath = path.join(process.cwd(), '.mlld/env');
  const globalPath = path.join(os.homedir(), '.mlld/env');
  
  const localEnvs = await scanEnvDir(localPath);
  const globalEnvs = await scanEnvDir(globalPath);
  
  if (isJson) {
    console.log(JSON.stringify({ local: localEnvs, global: globalEnvs }));
    return;
  }
  
  console.log('Available environments:\n');
  
  if (localEnvs.length > 0) {
    console.log(`Local (${localPath}):`);
    for (const env of localEnvs) {
      console.log(`  ${env.name.padEnd(15)} ${env.about || ''}`);
    }
    console.log();
  }
  
  if (globalEnvs.length > 0) {
    console.log(`Global (${globalPath}):`);
    for (const env of globalEnvs) {
      console.log(`  ${env.name.padEnd(15)} ${env.about || ''}`);
    }
    console.log();
  }
  
  const total = localEnvs.length + globalEnvs.length;
  console.log(`(${total} environment${total !== 1 ? 's' : ''} total)`);
}

async function scanEnvDir(dirPath: string): Promise<EnvInfo[]> {
  try {
    const entries = await fs.readdir(dirPath, { withFileTypes: true });
    const envs: EnvInfo[] = [];
    
    for (const entry of entries) {
      if (!entry.isDirectory()) continue;
      
      const manifestPath = path.join(dirPath, entry.name, 'module.yml');
      try {
        const manifest = await loadModuleManifest(manifestPath);
        if (manifest.type === 'environment') {
          envs.push({
            name: entry.name,
            about: manifest.about,
            hasCredentials: await checkKeychainCredentials(entry.name)
          });
        }
      } catch {
        // Skip invalid modules
      }
    }
    
    return envs;
  } catch {
    return [];
  }
}
```

### Show Credential Status

Optionally show whether credentials exist in keychain:
```
  claude-dev      Development Claude    [credentials: ✓]
  claude-new      New environment       [credentials: missing]
```

## Exit Criteria
- [ ] `mlld env list` shows local environments
- [ ] `mlld env list` shows global environments
- [ ] `mlld env list --json` outputs JSON
- [ ] Empty directories handled gracefully
- [ ] Non-environment modules ignored
- [ ] Credential status shown (if keychain available)

## Tests to Add
- Test with mock filesystem containing env modules
- Test JSON output format
- Test empty directory handling

## Dependencies
- Phase 1.1 (rename env → vars)
- Phase 5.1 (environment module type)

## Estimated Effort
2 hours


