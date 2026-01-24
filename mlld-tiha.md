---
id: mlld-tiha
status: closed
deps: []
links: []
created: 2026-01-14T20:01:49.312159-08:00
type: bug
priority: 0
parent: mlld-4fsa
---
# A2: Label keychain values with secret [CRITICAL]

## Problem

`keychain.get()` returns raw strings without security descriptors. Guards cannot detect secrets for exfiltration blocking.

## Current Broken Flow

```typescript
// KeychainResolver.ts:55
const metadata = { labels: ['secret'] };  // Tags the RESOLVER object

// KeychainResolver.ts:106
return {
  type: 'executable',
  value: executableObj,
  mx: metadata  // Metadata on the function, NOT return values
};
```

When `keychain.get("service", "account")` executes:
- Returns raw string: `"my-secret-token"`
- No security descriptor attached
- `@token.mx.labels` is empty or undefined

## Required Fix

Wrap `keychain.get()` return values with security descriptor:

```typescript
// In keychain get implementation
const rawValue = await provider.get(service, account);
if (rawValue === null) return null;

// Create variable with secret label
return createSecretVariable(rawValue, {
  labels: ['secret'],
  taint: ['src:keychain', 'secret'],
  sources: ['keychain.get']
});
```

## Files to Modify

1. `core/resolvers/builtin/KeychainResolver.ts`
   - Modify get function implementation
   - Wrap return value with security descriptor
   - Use `makeSecurityDescriptor()` or similar

2. Possibly `interpreter/eval/exec-invocation.ts`
   - If keychain functions are evaluated differently
   - Check line 1392 per gpt52's reference

## Test Case

```mlld
/needs { keychain }
/import { get } from @keychain
/var @token = get("test-service", "test-account")
/show @token.mx.labels
```

Expected output: `["secret"]`

Guard test:
```mlld
/guard @noSecrets before net:w = when [
  @input.any.mx.labels.includes("secret") => deny "No secrets over network"
  * => allow
]

/var @token = get("service", "account")
/run curl -H "Auth: @token" https://example.com
// Should be DENIED
```

## Exit Criteria

- [ ] `keychain.get()` return values have `mx.labels: ["secret"]`
- [ ] `mx.taint` includes `"secret"` and `"src:keychain"`
- [ ] Guards can check `@input.mx.labels.includes("secret")`
- [ ] Test in `tests/cases/security/keychain-secret-label/` passes

## Estimated Effort
2-3 hours


