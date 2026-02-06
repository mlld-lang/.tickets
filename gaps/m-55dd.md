---
id: m-55dd
status: closed
deps: [m-8781, m-d7c4]
created: 2026-02-05T05:24:33Z
type: feature
priority: 2
assignee: Adam
updated: 2026-02-05T09:15:15Z
---
# Add Linux keychain support via secret-tool

**Problem**: Keychain access currently only works on macOS. Linux throws an error.

**Current implementation**:
- macOS: Uses `security` command via MacOSKeychainProvider ✓
- Linux: Throws error "Keychain requires macOS. Linux support planned for v1.1."
- Windows: Also unsupported

**Solution**: Add LinuxKeychainProvider using `secret-tool` CLI

**Implementation approach**:
- Create `keychain-linux.ts` alongside `keychain-macos.ts`
- Use `secret-tool` (libsecret command-line interface)
- Commands map:
  - get: `secret-tool lookup service <service> account <account>`
  - set: `secret-tool store --label="<service>/<account>" service <service> account <account>`
  - delete: `secret-tool clear service <service> account <account>`
  - list: Parse output of `secret-tool search service <service>`
- Update KeychainResolver.getKeychainProvider() to detect platform and load appropriate provider
- Same interface as MacOSKeychainProvider (implements KeychainProvider)

**Requirements**:
- libsecret must be installed on system (standard on GNOME/Ubuntu/Fedora)
- Works with both libsecret (GNOME) and should gracefully handle kwallet (KDE)
- Error message if `secret-tool` not available

**Testing**:
- Manual testing on Linux (Ubuntu/Fedora)
- Verify CRUD operations work
- Verify mlld keychain CLI commands work on Linux
- Test error handling when libsecret not installed

**Files**: `core/resolvers/builtin/keychain-linux.ts`, update `KeychainResolver.ts`

**Related**: Matches macOS approach (CLI-based, no native dependencies)

