---
id: mlld-yrhq
status: closed
deps: []
links: []
created: 2026-01-14T20:03:44.274019-08:00
type: bug
priority: 2
parent: mlld-2ubc
---
# B5: Add platform guard for keychain in CLI [MEDIUM]

## Problem

CLI uses `MacOSKeychainProvider` directly without checking platform. On non-macOS, users get opaque spawn errors from the `security` command.

## Current Code (cli/commands/env.ts:8,189,281)

```typescript
import { MacOSKeychainProvider } from '...';

const provider = new MacOSKeychainProvider();
const token = await provider.get('mlld-env', name);
// On Linux: spawn error from missing 'security' command
```

## Required Fix

```typescript
import { getKeychainProvider, KeychainUnavailableError } from '...';

try {
  const provider = getKeychainProvider();  // Throws on non-macOS
  const token = await provider.get('mlld-env', name);
} catch (err) {
  if (err instanceof KeychainUnavailableError) {
    console.error(chalk.red('Keychain is only available on macOS.'));
    console.error('On Linux, keychain support is planned for v1.1.');
    process.exit(1);
  }
  throw err;
}
```

Or use the factory function that already exists:

```typescript
// In core/resolvers/builtin/keychain.ts or similar
export function getKeychainProvider(): KeychainProvider {
  if (process.platform !== 'darwin') {
    throw new KeychainUnavailableError(
      'Keychain requires macOS. Linux support planned for v1.1.'
    );
  }
  return new MacOSKeychainProvider();
}
```

## Files to Modify

1. `cli/commands/env.ts:8`
   - Import getKeychainProvider instead of MacOSKeychainProvider
   
2. `cli/commands/env.ts:189,281`
   - Use factory function with proper error handling

3. Possibly create factory if it doesn't exist:
   - `core/resolvers/builtin/keychain.ts`

## Exit Criteria

- [ ] Clear error on non-macOS: "Keychain requires macOS"
- [ ] Mentions Linux support planned for v1.1
- [ ] No opaque spawn errors
- [ ] Works correctly on macOS

## Estimated Effort
1 hour


