---
id: mlld-uz8v
status: closed
deps: [mlld-94a7]
links: []
created: 2026-01-14T20:04:41.060199-08:00
type: task
priority: 1
parent: mlld-j5bw
---
# C2: Generalized capability gating framework

## Summary

Generalize the keychain gating pattern (A3) to all capabilities: network, filesystem, sh, cmd.

## Design

Centralized capability checker that gates access based on /needs and /wants declarations:

```typescript
// core/capabilities/CapabilityGate.ts
export class CapabilityGate {
  constructor(private env: Environment) {}
  
  requireCapability(capability: string): void {
    const needs = this.env.getModuleNeeds();
    const wantsTier = this.env.getGrantedWantsTier();
    
    // Check if declared in /needs
    if (this.isDeclaredInNeeds(capability, needs)) {
      return; // Allowed
    }
    
    // Check if granted via /wants tier
    if (this.isGrantedByTier(capability, wantsTier)) {
      return; // Allowed
    }
    
    throw new MlldCapabilityError(
      `Capability '${capability}' requires declaration in /needs or /wants`,
      { code: 'CAPABILITY_UNDECLARED' }
    );
  }
  
  private isDeclaredInNeeds(cap: string, needs: NeedsDeclaration): boolean {
    switch (cap) {
      case 'keychain': return needs.keychain === true;
      case 'network': return needs.network === true;
      case 'filesystem': return needs.filesystem === true;
      case 'sh': return needs.sh === true;
      case 'cmd': return needs.cmd !== undefined;
      default: return false;
    }
  }
}
```

## Integration Points

### 1. Keychain (from A3)
```typescript
// KeychainResolver.resolve()
gate.requireCapability('keychain');
```

### 2. Network Operations
```typescript
// Before curl, fetch, http operations
gate.requireCapability('network');
```

### 3. Filesystem Operations
```typescript
// Before file read/write outside project
gate.requireCapability('filesystem');
```

### 4. Shell Access
```typescript
// Before sh, bash commands
gate.requireCapability('sh');
```

### 5. Command Execution
```typescript
// Before /run command
// Check if specific command is in needs.cmd
gate.requireCommand(commandName);
```

## Files to Create/Modify

1. **Create**: `core/capabilities/CapabilityGate.ts`
   - Central gating logic

2. **Modify**: `interpreter/env/Environment.ts`
   - Add getCapabilityGate() method

3. **Modify**: Various evaluators
   - `interpreter/eval/run.ts` - check cmd/sh
   - Keychain resolver - use gate
   - Network operations - use gate

## Phased Rollout

1. First: Keychain (already gated in A3)
2. Then: sh/bash commands
3. Then: cmd (specific commands)
4. Later: network, filesystem (may be more disruptive)

## Exit Criteria

- [ ] CapabilityGate class implemented
- [ ] All capabilities checked against /needs and /wants
- [ ] Clear error messages for undeclared capabilities
- [ ] Tests verify gating for each capability type

## Estimated Effort
4-6 hours for framework + 1-2h per capability integration


