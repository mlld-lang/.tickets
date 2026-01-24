---
id: mlld-94a7
status: closed
deps: []
links: []
created: 2026-01-14T20:02:07.429809-08:00
type: bug
priority: 0
parent: mlld-4fsa
---
# A3: Gate keychain access by /needs declaration [CRITICAL]

## Problem

Any script can `import { get } from @keychain` without declaring `/needs { keychain }`. No capability enforcement exists.

## Current Broken Flow

```mlld
// No /needs declaration - should fail but works!
/import { get } from @keychain
/var @token = get("mlld-env", "production")
// Successfully steals production credentials
```

## Required Fix

Check for `/needs { keychain }` before allowing keychain access:

```typescript
// In KeychainResolver.resolve()
resolve(path: string, env: Environment, context: ResolveContext) {
  // Check needs declaration
  const needs = env.getModuleNeeds();
  if (!needs?.keychain) {
    throw new MlldNeedsError(
      '@keychain requires /needs { keychain } declaration',
      { code: 'NEEDS_UNMET' }
    );
  }
  
  // Continue with normal resolution...
}
```

## Files to Modify

1. `core/resolvers/builtin/KeychainResolver.ts`
   - Add needs check at start of resolve()
   - Import MlldNeedsError from core/errors

2. Verify `env.getModuleNeeds()` returns the right data
   - Check `interpreter/env/Environment.ts`
   - Needs should be recorded before imports are evaluated

## Evaluation Order

```mlld
/needs { keychain }           // Line 1 - evaluated, needs recorded
/import { get } from @keychain // Line 2 - resolver checks needs ✓
```

```mlld
/import { get } from @keychain // Line 1 - resolver checks needs ✗
/needs { keychain }           // Line 2 - too late!
```

The check should fail if needs aren't declared BEFORE the import.

## Test Cases

```mlld
// tests/cases/exceptions/keychain-needs-required/example.md
/import { get } from @keychain
/var @token = get("service", "account")
```

```mlld
// tests/cases/exceptions/keychain-needs-required/error.md
@keychain requires /needs { keychain } declaration
```

```mlld
// tests/cases/security/keychain-with-needs/example.md
/needs { keychain }
/import { get } from @keychain
/var @token = get("test", "test")
/show "got token"
```

## Exit Criteria

- [ ] `@keychain` import fails without `/needs { keychain }`
- [ ] Error message is clear: "requires /needs { keychain }"
- [ ] Works correctly when /needs IS declared
- [ ] Test in `tests/cases/exceptions/keychain-needs-required/` passes

## Estimated Effort
2 hours

## Note

This is the first capability gating implementation. Pattern established here will be reused in Track C for all capabilities.


