---
id: mlld-8ohj
status: closed
deps: [mlld-ov8w]
links: []
created: 2026-01-14T15:17:28.726989-08:00
type: task
priority: 0
parent: mlld-x60s
---
# Phase 2.3: Validate /needs at module load time [SECURITY]

## Summary
Add validation to fail fast when a module's `/needs` declarations cannot be satisfied by the runtime.

## Security Impact
Modules should fail immediately if required capabilities are unavailable, not silently continue with reduced functionality.

## Context
From audit-needs-wants.md: Currently `/needs` only records requirements but never validates them. Location: `interpreter/eval/needs.ts:10-26`

```typescript
export async function evaluateNeeds(directive, env) {
  const needs = normalizeNeedsDeclaration(needsRaw);
  env.recordModuleNeeds(needs);  // Just records, no validation!
  return { value: undefined };
}
```

## Implementation

### Files to Modify

1. **`interpreter/eval/needs.ts:10-26`**
   Add validation step after recording:
   ```typescript
   export async function evaluateNeeds(directive, env) {
     const needs = normalizeNeedsDeclaration(needsRaw);
     env.recordModuleNeeds(needs);
     
     // NEW: Validate needs can be satisfied
     const unmetNeeds = validateNeeds(needs, env.getSystemCapabilities());
     if (unmetNeeds.length > 0) {
       throw new MlldNeedsError(
         `Module requires capabilities not available: ${unmetNeeds.join(', ')}`
       );
     }
     
     return { value: undefined };
   }
   ```

2. **Create `core/errors/MlldNeedsError.ts`** (or add to existing error types)
   ```typescript
   export class MlldNeedsError extends MlldError {
     constructor(message: string) {
       super(message, 'NEEDS_UNMET');
     }
   }
   ```

3. **Add `getSystemCapabilities()` to Environment**
   Returns what capabilities are actually available:
   - `keychain`: true if macOS (darwin)
   - `sh/bash`: true if shell available
   - `network`: true (always available?)
   - `filesystem`: true (always available?)

### Validation Logic

```typescript
function validateNeeds(needs: NeedsDeclaration, caps: SystemCapabilities): string[] {
  const unmet: string[] = [];
  
  if (needs.keychain && !caps.keychain) {
    unmet.push('keychain (macOS only in v1)');
  }
  
  // For commands, optionally check existence
  if (needs.cmd) {
    for (const cmd of needs.cmd) {
      // Could use 'which' or just trust runtime to fail
    }
  }
  
  return unmet;
}
```

## Exit Criteria
- [ ] Module with `/needs { keychain }` fails on non-macOS with clear error
- [ ] Error message explains what's unavailable and why
- [ ] Modules without unmet needs load normally
- [ ] All existing tests pass (update any that expect silent needs recording)
- [ ] Build succeeds with no type errors

## Tests to Add
Create `tests/cases/exceptions/needs-unmet-keychain/`
```
example.md:
  /needs { keychain }
  /show "This should not run"
error.md:
  Module requires capabilities not available: keychain
```
Note: This test should only run on non-macOS, or mock system capabilities.

Create test helper to mock system capabilities for testing unmet needs scenarios.

## Dependencies
- Phase 2.2 (keychain capability in grammar)

## Estimated Effort
3 hours


