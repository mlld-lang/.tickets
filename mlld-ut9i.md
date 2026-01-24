---
id: mlld-ut9i
status: closed
deps: [mlld-esjy]
links: []
created: 2026-01-14T15:19:38.282454-08:00
type: task
priority: 1
parent: mlld-x60s
---
# Phase 4.2: macOS keychain implementation

## Summary
Implement macOS keychain access via the `security` CLI command.

## Context
macOS provides the `security` command for keychain operations. This is the only platform supported in v1.

## Implementation

### Create macOS Provider

Location: `interpreter/builtins/keychain-macos.ts`

```typescript
import { spawn } from 'child_process';
import type { KeychainProvider } from './keychain';

export class MacOSKeychainProvider implements KeychainProvider {
  
  async get(service: string, account: string): Promise<string | null> {
    try {
      // security find-generic-password -s <service> -a <account> -w
      const result = await this.exec('security', [
        'find-generic-password',
        '-s', service,
        '-a', account,
        '-w'  // Output password only
      ]);
      return result.trim();
    } catch (error) {
      // "The specified item could not be found" = not found
      if (error.message.includes('could not be found')) {
        return null;
      }
      throw error;
    }
  }
  
  async set(service: string, account: string, value: string): Promise<void> {
    // -U flag updates if exists, creates if not
    await this.exec('security', [
      'add-generic-password',
      '-s', service,
      '-a', account,
      '-w', value,
      '-U'  // Update existing or create new
    ]);
  }
  
  async delete(service: string, account: string): Promise<void> {
    try {
      await this.exec('security', [
        'delete-generic-password',
        '-s', service,
        '-a', account
      ]);
    } catch (error) {
      // Ignore "not found" errors on delete
      if (!error.message.includes('could not be found')) {
        throw error;
      }
    }
  }
  
  private async exec(command: string, args: string[]): Promise<string> {
    return new Promise((resolve, reject) => {
      const proc = spawn(command, args);
      let stdout = '';
      let stderr = '';
      
      proc.stdout.on('data', (data) => { stdout += data; });
      proc.stderr.on('data', (data) => { stderr += data; });
      
      proc.on('close', (code) => {
        if (code === 0) {
          resolve(stdout);
        } else {
          reject(new Error(stderr || `Command failed with code ${code}`));
        }
      });
    });
  }
}
```

### Security Considerations

1. **Keychain Prompts**: macOS may prompt user for keychain access
   - First access triggers system dialog
   - User can "Always Allow" for mlld
   - This is expected behavior

2. **Token Security**
   - Token only in memory during execution
   - Not written to disk (except keychain encryption)
   - Process memory cleared on exit

3. **Service Naming**
   - Use `mlld-env` as service prefix for environment tokens
   - Account = environment name

### Platform Check

```typescript
export function getKeychainProvider(): KeychainProvider {
  if (process.platform !== 'darwin') {
    throw new MlldNeedsError(
      'Keychain requires macOS. Linux support planned for v1.1.'
    );
  }
  return new MacOSKeychainProvider();
}
```

## Exit Criteria
- [ ] Can store token: `keychain.set("mlld-env", "test", "secret123")`
- [ ] Can retrieve token: `keychain.get("mlld-env", "test")` returns "secret123"
- [ ] Can delete token: `keychain.delete("mlld-env", "test")`
- [ ] Non-existent key returns null (not error)
- [ ] Keychain access prompt appears on first use (expected)
- [ ] Error on non-macOS platforms with clear message
- [ ] Integration test with actual keychain (manual test)

## Tests to Add

Integration tests require actual keychain access:
```bash
# Manual test script
npm run test:keychain -- --platform darwin
```

Create mock provider for unit tests:
```typescript
class MockKeychainProvider implements KeychainProvider {
  private store = new Map<string, string>();
  // ... mock implementation
}
```

## Dependencies
- Phase 4.1 (keychain built-in functions)

## Estimated Effort
3 hours


