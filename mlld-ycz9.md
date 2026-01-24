---
id: mlld-ycz9
status: closed
deps: []
links: []
created: 2026-01-14T20:03:17.59939-08:00
type: bug
priority: 2
parent: mlld-2ubc
---
# B3: Validate environment name for path traversal [MEDIUM]

## Problem

`mlld env capture ../x` can write outside `.mlld/env/` via path traversal.

## Attack

```bash
mlld env capture "../../../tmp/evil"
# Creates files at /tmp/evil/ instead of .mlld/env/
```

## Required Fix

Validate environment name before use:

```typescript
function validateEnvName(name: string): void {
  // Reject path separators
  if (name.includes('/') || name.includes('\\')) {
    throw new Error('Environment name cannot contain path separators');
  }
  
  // Reject path traversal
  if (name.includes('..')) {
    throw new Error('Environment name cannot contain ".."');
  }
  
  // Reject hidden files
  if (name.startsWith('.')) {
    throw new Error('Environment name cannot start with "."');
  }
  
  // Validate characters (alphanumeric, dash, underscore)
  if (!/^[a-zA-Z0-9_-]+$/.test(name)) {
    throw new Error('Environment name can only contain letters, numbers, dashes, and underscores');
  }
}
```

## Files to Modify

1. `cli/commands/env.ts`
   - Add validateEnvName() function
   - Call at start of capture, spawn, shell commands
   - Around lines 143, 253, 309

## Test Cases

```bash
mlld env capture "my-env"     # ✓ Valid
mlld env capture "../escape"   # ✗ Path traversal
mlld env capture "foo/bar"     # ✗ Path separator
mlld env capture ".hidden"     # ✗ Hidden file
mlld env capture "env name"    # ✗ Space in name
```

## Exit Criteria

- [ ] Path traversal attempts rejected with clear error
- [ ] Only valid env names accepted
- [ ] Validation applied to capture, spawn, shell commands
- [ ] Tests verify rejection of malicious names

## Estimated Effort
1 hour


