---
id: mlld-h138
status: closed
deps: []
links: []
created: 2026-01-15T10:54:47.018358-08:00
type: task
priority: 1
parent: mlld-urik
---
# C2-GAP: Unify and complete capability gating

## Problem

Capability gating is incomplete and scattered:
- ✅ Keychain: Gated in ImportDirectiveEvaluator.ts:372
- ✅ Network: Gated in Environment.ts fetchURL methods
- ❌ Shell/cmd: NOT gated - modules can /run shell commands without /needs { sh }
- ❌ Filesystem: NOT gated - modules can access files without declaration

No unified CapabilityGate class exists - checks are scattered.

## Current State

From audit:
- `core/policy/capabilities.ts` has checker functions but they're not enforced
- `isShellCapabilityDeclared()` exists but not called before shell execution
- `isCommandCapabilityDeclared()` exists but not called before /run

## Required Fix

### Option A: Unified CapabilityGate (Recommended)

Create `core/capabilities/CapabilityGate.ts`:
```typescript
export class CapabilityGate {
  constructor(private env: Environment) {}
  
  requireCapability(cap: 'sh' | 'cmd' | 'network' | 'filesystem' | 'keychain'): void {
    const needs = this.env.getModuleNeeds();
    const tier = this.env.getGrantedWantsTier();
    
    if (!this.isCapabilityGranted(cap, needs, tier)) {
      throw new MlldCapabilityError(
        `${cap} access requires /needs { ${cap} } declaration`,
        { code: 'CAPABILITY_UNDECLARED' }
      );
    }
  }
}
```

### Option B: Add checks to existing locations

Add gating calls to:
- `interpreter/eval/run.ts` - before executing commands
- File operation evaluators - before filesystem access

## Files to Modify

1. Create or enhance: `core/capabilities/CapabilityGate.ts`
2. `interpreter/eval/run.ts` - gate sh/cmd before execution
3. File operation paths - gate filesystem access
4. Wire gate into Environment for easy access

## Test Cases

```mlld
// Should fail - no /needs { sh }
/run echo "hello"
// Error: sh access requires /needs { sh } declaration

// Should pass
/needs { sh }
/run echo "hello"
// Output: hello
```

## Exit Criteria

- [ ] /run commands require /needs { sh } or /needs { cmd: [...] }
- [ ] Filesystem operations require /needs { filesystem } (or define safe defaults)
- [ ] Unified gating mechanism exists
- [ ] Tests verify all capabilities are gated

## Estimated Effort
4-6 hours


