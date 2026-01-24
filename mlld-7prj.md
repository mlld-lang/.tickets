---
id: mlld-7prj
status: open
deps: []
links: []
created: 2026-01-15T11:04:27.639285-08:00
type: bug
priority: 1
parent: mlld-urik
---
# HIGH: Environment module type not installable

## Problem

Environment modules cannot be installed or scaffolded via `mlld module`:
- ModuleInstaller valid types exclude 'environment'
- CLI help/type list omits it

## Current State

From gpt52:
- `core/registry/ModuleInstaller.ts:376` - valid types don't include 'environment'
- `cli/commands/init-module.ts:20` - type options don't include 'environment'
- `cli/commands/init-module.ts:804` - help text doesn't mention environment

## Impact

```bash
mlld module init --type environment my-env
# Error: Invalid type 'environment'

mlld module install @someone/claude-env
# May fail if module.yml has type: environment
```

## Required Fix

### 1. Add to valid types

```typescript
// ModuleInstaller.ts
const VALID_MODULE_TYPES = ['library', 'app', 'command', 'skill', 'environment'];
```

### 2. Add to CLI options

```typescript
// init-module.ts
const TYPE_OPTIONS = [
  'library',
  'app', 
  'command',
  'skill',
  'environment'  // ADD
];
```

### 3. Update help text

```typescript
// init-module.ts help
Types:
  library      - Reusable mlld code
  app          - Standalone application
  command      - CLI command extension
  skill        - AI assistant skill
  environment  - AI agent environment configuration  // ADD
```

### 4. Environment-specific scaffolding

```typescript
if (type === 'environment') {
  // Generate environment-specific template
  // - index.mld with @spawn, @shell exports
  // - module.yml with type: environment
  // - .claude/ directory structure
}
```

## Exit Criteria

- [ ] `mlld module init --type environment` works
- [ ] `mlld module install` accepts type: environment modules
- [ ] Help text documents environment type
- [ ] Scaffold generates proper environment structure

## Estimated Effort
2-3 hours


