---
id: mlld-7jp9
status: closed
deps: []
links: []
created: 2026-01-14T20:05:18.576161-08:00
type: task
priority: 2
parent: mlld-j5bw
---
# C4: mlld update-needs command for capability detection

## Summary

Extend `mlld validate` (formerly `mlld analyze`) to detect capability requirements and output suggested `/needs` declaration.

## Current State

`mlld validate` and `DependencyDetector` already detect:
- ✅ `sh`, `js`/`node`, `py` runtimes
- ✅ Shell commands (curl, wget, git, etc.)
- ✅ Node.js and Python packages

**Missing capability detection:**
- ❌ `@keychain` imports → `keychain` capability
- ❌ Network commands → `network` capability
- ❌ `/needs` declaration syntax output

## Required Changes

### 1. Add Keychain Detection

In `DependencyDetector` or `analyze.ts`:

```typescript
function detectKeychainUsage(ast: MlldNode[]): boolean {
  let usesKeychain = false;
  walkAST(ast, (node) => {
    if (node.type === 'Directive' && node.kind === 'import') {
      const importNode = node as any;
      if (importNode.raw?.path === '@keychain') {
        usesKeychain = true;
      }
    }
  });
  return usesKeychain;
}
```

### 2. Map Commands to Capabilities

```typescript
const NETWORK_COMMANDS = new Set(['curl', 'wget', 'fetch', 'http', 'nc', 'ssh', 'scp']);

function detectNetworkCapability(shellCommands: string[]): boolean {
  return shellCommands.some(cmd => NETWORK_COMMANDS.has(cmd));
}
```

### 3. Add /needs Output Format

```typescript
function formatNeedsDeclaration(caps: DetectedCapabilities): string {
  const parts: string[] = [];
  
  if (caps.keychain) parts.push('keychain');
  if (caps.network) parts.push('network');
  if (caps.sh) parts.push('sh');
  if (caps.cmd.length > 0) {
    parts.push(`cmd: [${caps.cmd.join(', ')}]`);
  }
  
  return `/needs { ${parts.join(', ')} }`;
}
```

### 4. Update CLI Output

```
$ mlld validate my-module.mld

Valid module

executables  @myFunc
exports      @myFunc
imports      @keychain (get)
needs        sh, keychain

Suggested /needs declaration:
  /needs { keychain, sh, cmd: [curl, jq] }
```

## Files to Modify

1. `cli/commands/analyze.ts`
   - Add keychain detection to `extractNeeds()`
   - Add network capability mapping
   - Add `/needs` declaration output

2. `core/utils/dependency-detector.ts`
   - Add `detectCapabilities()` method
   - Map detected commands to capabilities

## Exit Criteria

- [ ] `@keychain` import detected as `keychain` capability
- [ ] `curl`/`wget` commands flagged as `network` capability
- [ ] Output includes suggested `/needs { ... }` declaration
- [ ] Works with `mlld validate --format json` for tooling

## Estimated Effort
3-4 hours (extending existing code, not building from scratch)


