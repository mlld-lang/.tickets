---
id: mlld-b2nu
status: open
deps: []
links: []
created: 2026-01-15T10:55:02.432488-08:00
type: task
priority: 1
parent: mlld-urik
---
# Capability inference for policy checking (was: C4-GAP analyze detection)

## Problem

`mlld analyze` (now `mlld validate`) has incomplete capability detection:
- ✅ Detects: cmd, node, py
- ❌ Missing: keychain, network, filesystem

Importantly, `cli/commands/update-needs.ts` already HAS detection for keychain and network, but `analyze.ts` doesn't use it.

## Current State

**analyze.ts NeedsInfo interface (lines 43-47):**
```typescript
export interface NeedsInfo {
  cmd?: string[];
  node?: string[];
  py?: string[];
  // Missing: keychain, network, filesystem
}
```

**update-needs.ts has (lines 101-158):**
- `detectKeychainImport()` - Detects @keychain resolver imports
- `detectNetworkUsage()` - Detects fetch() in JS code

## Required Fix

### 1. Extend NeedsInfo interface

```typescript
export interface NeedsInfo {
  cmd?: string[];
  node?: string[];
  py?: string[];
  keychain?: boolean;    // ADD
  network?: boolean;     // ADD
  filesystem?: boolean;  // ADD (optional)
}
```

### 2. Add detection in extractNeeds()

```typescript
// Detect keychain
walkAST(ast, (node) => {
  if (node.type === 'Directive' && node.kind === 'import') {
    if (node.raw?.path === '@keychain') {
      needs.keychain = true;
    }
  }
});

// Detect network (from update-needs.ts pattern)
// Check for curl/wget in shell commands
// Check for fetch() in JS code
```

### 3. Add /needs output format

```typescript
function formatNeedsDeclaration(needs: NeedsInfo): string {
  const parts: string[] = [];
  if (needs.keychain) parts.push('keychain');
  if (needs.network) parts.push('network');
  if (needs.cmd?.length) parts.push('sh');  // or specific commands
  // ...
  return `/needs { ${parts.join(', ')} }`;
}
```

### 4. Update CLI output

```
$ mlld validate module.mld

Valid module
...
Suggested /needs declaration:
  /needs { keychain, network, sh }
```

## Files to Modify

1. `cli/commands/analyze.ts`
   - Extend NeedsInfo interface
   - Add keychain/network detection to extractNeeds()
   - Add /needs syntax output

2. Consider extracting detection logic from `update-needs.ts` to share

## Exit Criteria

- [ ] @keychain imports detected as keychain capability
- [ ] Network commands (curl, wget, fetch) detected as network capability
- [ ] Output includes suggested `/needs { ... }` declaration
- [ ] `--format json` includes new capability fields

## Estimated Effort
3-4 hours


