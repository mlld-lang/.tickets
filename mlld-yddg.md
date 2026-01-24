---
id: mlld-yddg
status: open
deps: []
links: []
created: 2026-01-19T22:41:52.078595-08:00
type: task
priority: 2
---
# Keychain providers (system, 1password)

## Summary

Implement keychain provider configuration from spec v4 Section 3.7.

## Policy Syntax

```mlld
policy {
  keychain: {
    provider: "system",              // or: "1password"
    allow: ["mlld-env/*", "company/*"],
    deny: ["system/*"]
  }
}
```

## Provider Interface

```typescript
// core/keychain/types.ts

interface KeychainProvider {
  name: string;
  
  // Check if provider is available
  isAvailable(): Promise<boolean>;
  
  // Get a secret by path
  get(path: string): Promise<string | undefined>;
  
  // List available paths (for validation)
  list(pattern: string): Promise<string[]>;
}

interface KeychainConfig {
  provider: string;
  allow?: string[];
  deny?: string[];
}
```

## Provider Implementations

### SystemKeychainProvider

Uses OS keychain via existing KeychainResolver.

```typescript
class SystemKeychainProvider implements KeychainProvider {
  name = "system";
  
  async isAvailable(): Promise<boolean> {
    // Check if security CLI (macOS) or equivalent exists
  }
  
  async get(path: string): Promise<string | undefined> {
    // Existing KeychainResolver.get() logic
  }
  
  async list(pattern: string): Promise<string[]> {
    // security dump-keychain or equivalent
  }
}
```

### OnePasswordProvider

Uses 1Password CLI (`op`).

```typescript
class OnePasswordProvider implements KeychainProvider {
  name = "1password";
  
  async isAvailable(): Promise<boolean> {
    // Check if `op` CLI exists and is signed in
    return this.exec("op", ["--version"]).success;
  }
  
  async get(path: string): Promise<string | undefined> {
    // op:// URLs: op://vault/item/field
    // Convert mlld path to op:// format
    const opPath = this.toOpPath(path);
    return this.exec("op", ["read", opPath]);
  }
  
  async list(pattern: string): Promise<string[]> {
    // op item list --format=json
  }
  
  private toOpPath(mlldPath: string): string {
    // mlld: "mlld-env/claude" → op: "op://mlld-env/claude/password"
    // or: "vault/item" → "op://vault/item/password"
  }
}
```

## Access Control

Before fetching, check against allow/deny:

```typescript
function canAccessPath(path: string, config: KeychainConfig): boolean {
  // Check deny first (deny wins)
  if (config.deny?.some(p => matchGlob(path, p))) {
    return false;
  }
  
  // If allow specified, must match
  if (config.allow && \!config.allow.some(p => matchGlob(path, p))) {
    return false;
  }
  
  return true;
}
```

## Integration with policy.auth

```mlld
policy {
  auth: {
    claude: { from: "keychain:mlld-env/claude", as: "ANTHROPIC_API_KEY" }
  },
  keychain: {
    provider: "1password",
    allow: ["mlld-env/*"]
  }
}
```

When resolving `keychain:mlld-env/claude`:
1. Get provider from `policy.keychain.provider`
2. Check path against allow/deny
3. Call `provider.get("mlld-env/claude")`

## Files to Modify

1. `core/keychain/types.ts` - Provider interface
2. `core/keychain/SystemKeychainProvider.ts` - Refactor existing
3. `core/keychain/OnePasswordProvider.ts` - New
4. `core/keychain/KeychainResolver.ts` - Use provider
5. `core/policy/union.ts` - Add keychain config parsing

## Exit Criteria

- [ ] Provider interface defined
- [ ] SystemKeychainProvider works (refactored from existing)
- [ ] OnePasswordProvider works with `op` CLI
- [ ] Allow/deny patterns enforced
- [ ] Policy keychain config parsed
- [ ] Tests for both providers

## References

- spec-security-2026-v4.md Section 3.7
- 1Password CLI docs: https://developer.1password.com/docs/cli

## Estimated Effort

~5 hours


