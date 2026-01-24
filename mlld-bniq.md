---
id: mlld-bniq
status: closed
deps: [mlld-ut9i]
links: []
created: 2026-01-14T15:19:56.673932-08:00
type: task
priority: 1
parent: mlld-x60s
---
# Phase 5.1: Add environment module type

## Summary
Add `environment` as a valid module type in the mlld registry system.

## Context
Environment modules package everything needed to spawn an AI agent with credentials, config, and security policy. They need their own module type for proper installation paths and validation.

## Implementation

### 1. Add to ModuleType

Location: `core/registry/types.ts:80-130`

```typescript
// Current
export type ModuleType = 'library' | 'app' | 'command' | 'skill';

// Change to
export type ModuleType = 'library' | 'app' | 'command' | 'skill' | 'environment';
```

### 2. Add Installation Paths

```typescript
export const MODULE_TYPE_PATHS: Record<ModuleType, { local: string; global: string }> = {
  library: { local: '.mlld/lib', global: '.mlld/lib' },
  app: { local: '.mlld/apps', global: '.mlld/apps' },
  command: { local: '.mlld/commands', global: '.mlld/commands' },
  skill: { local: '.mlld/skills', global: '.mlld/skills' },
  // ADD THIS
  environment: { local: '.mlld/env', global: '.mlld/env' }
};
```

### 3. Update Module Manifest Schema

Environment modules should have:
```yaml
name: claude-dev
author: alice
type: environment
about: "Development Claude configuration"
version: 1.0.0

# Environment-specific fields
entry: index.mld
```

### 4. Required Exports for Environment Modules

Environment modules must export at least one of:
- `@spawn(prompt)` - Run agent with prompt
- `@shell()` - Interactive agent session

Optionally:
- `@mcpConfig()` - MCP server configuration

## Exit Criteria
- [ ] `type: environment` accepted in module.yml
- [ ] Environment modules install to `.mlld/env/`
- [ ] Module validation checks for @spawn or @shell export
- [ ] `mlld module list --type environment` shows env modules
- [ ] All existing module tests pass
- [ ] Build succeeds

## Tests to Add

Create test module:
```
tests/modules/test-env/
  module.yml:
    name: test-env
    type: environment
    entry: index.mld
  index.mld:
    /needs { keychain }
    /exe @spawn(prompt) = run echo "@prompt"
    /export { @spawn }
```

Test that module installs correctly and type is recognized.

## Dependencies
- Phase 4 (keychain) - env modules typically need keychain

## Estimated Effort
30 minutes


