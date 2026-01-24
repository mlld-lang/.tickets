---
id: mlld-esjy
status: closed
deps: [mlld-8ohj]
links: []
created: 2026-01-14T15:19:14.457659-08:00
type: task
priority: 1
parent: mlld-x60s
---
# Phase 4.1: Keychain built-in functions

## Summary
Implement `keychain.get()`, `keychain.set()`, `keychain.delete()` as mlld built-in functions.

## Context
Environment modules need secure credential storage. The keychain capability provides access to the system keychain with automatic `secret` labeling.

## Implementation

### 1. Create Keychain Interface

Location: `interpreter/builtins/keychain.ts`

```typescript
export interface KeychainProvider {
  get(service: string, account: string): Promise<string | null>;
  set(service: string, account: string, value: string): Promise<void>;
  delete(service: string, account: string): Promise<void>;
}

// Platform-specific implementations
let provider: KeychainProvider | null = null;

export function getKeychainProvider(): KeychainProvider {
  if (!provider) {
    if (process.platform === 'darwin') {
      provider = new MacOSKeychainProvider();
    } else {
      throw new MlldNeedsError('Keychain not available on this platform (macOS only in v1)');
    }
  }
  return provider;
}
```

### 2. Register Built-in Functions

Location: `interpreter/builtins/index.ts` or similar

```typescript
// keychain.get(service, account) -> string | null (with secret label)
registerBuiltin('keychain.get', async (args, env) => {
  // Check /needs { keychain } was declared
  if (!env.getModuleNeeds().keychain) {
    throw new MlldSecurityError('keychain.get requires /needs { keychain }');
  }
  
  const [service, account] = args;
  const value = await getKeychainProvider().get(service, account);
  
  if (value === null) return null;
  
  // Return with secret label
  return makeSecretValue(value, {
    labels: ['secret'],
    taint: ['src:keychain'],
    sources: ['keychain.get']
  });
});

// keychain.set(service, account, value) -> void
registerBuiltin('keychain.set', async (args, env) => {
  if (!env.getModuleNeeds().keychain) {
    throw new MlldSecurityError('keychain.set requires /needs { keychain }');
  }
  
  const [service, account, value] = args;
  await getKeychainProvider().set(service, account, value);
  return undefined;
});

// keychain.delete(service, account) -> void  
registerBuiltin('keychain.delete', async (args, env) => {
  if (!env.getModuleNeeds().keychain) {
    throw new MlldSecurityError('keychain.delete requires /needs { keychain }');
  }
  
  const [service, account] = args;
  await getKeychainProvider().delete(service, account);
  return undefined;
});
```

### 3. Secret Label Handling

Values from `keychain.get()` automatically get `secret` label:
- Tracked through all transformations
- Guards can block secret data from being shown/output/sent over network
- This is the foundation for token protection

## Exit Criteria
- [ ] `keychain.get("service", "account")` returns string or null
- [ ] Returned value has `secret` label
- [ ] `keychain.set("service", "account", "value")` stores credential
- [ ] `keychain.delete("service", "account")` removes credential
- [ ] Calling without `/needs { keychain }` throws error
- [ ] Secret label propagates through transformations
- [ ] All tests pass
- [ ] Build succeeds

## Tests to Add

Create `tests/cases/security/keychain-needs-required/`
```
example.md:
  >> No /needs { keychain } declaration
  /var @token = keychain.get("test", "test")
error.md:
  keychain.get requires /needs { keychain }
```

Create `tests/cases/security/keychain-secret-label/`
```
example.md:
  /needs { keychain }
  /var @token = keychain.get("test", "test")
  /show @token.mx.labels
expected.md:
  ["secret"]
```
Note: May need mock keychain for testing.

## Dependencies
- Phase 2.2 (keychain capability in grammar)
- Phase 2.3 (/needs validation)

## Estimated Effort
2 hours


