---
id: m-9a3e
status: open
deps: []
links: []
created: 2026-03-09T00:18:00Z
type: feature
priority: 2
assignee: Adam
tags: [infrastructure, keychain]
---
# Headless keychain fallback for VPS/server environments

## Problem

mlld's `@keychain` resolver shells out to platform-native tools:
- **macOS**: `security` CLI (Keychain Access) — works headless, no issues
- **Linux**: `secret-tool` CLI (libsecret/D-Bus) — requires a running keyring daemon with a D-Bus session, which doesn't exist on headless VPS/server environments

On a Linux VPS without gnome-keyring or another Secret Service provider, `secret-tool` is unavailable and mlld throws `KEYCHAIN_UNAVAILABLE`. This means any mlld script using `@keychain` simply won't run on a headless server.

This is the same problem `gh` CLI, VS Code, and every other tool that uses system keyrings hit. `gh` solves it by silently falling back to a plaintext config file.

## Current behavior

`KeychainResolver.ts` → `getKeychainProvider()` checks platform, checks `isSecretToolAvailable()`, throws if not. No fallback path.

## Options

### 1. File-based fallback provider
Add a `FileKeychainProvider` that stores secrets in `~/.config/mlld/secrets.json` (or similar) with `0600` permissions. Used automatically when `secret-tool` isn't available.

**Pros**: Scripts using `@keychain` just work everywhere. Matches what `gh` does.
**Cons**: Secrets on disk in a JSON file. The threat model is "don't leave them world-readable" which is reasonable for a VPS (the secrets are already on the machine), but it's a step down from a real keyring. Users may not realize they're in fallback mode.

### 2. Explicit opt-in file store
Same as above but require the user to explicitly enable it (e.g., `mlld keychain --insecure-storage` or a config flag). Refuse to silently downgrade.

**Pros**: No surprises. User acknowledges the tradeoff.
**Cons**: Scripts still break by default on headless Linux until the user opts in. More friction.

### 3. Env var bridge
Allow `@keychain` lookups to fall back to environment variables using a naming convention (e.g., `keychain.get("mlld-box-myapp", "ANTHROPIC_API_KEY")` checks `$ANTHROPIC_API_KEY`).

**Pros**: Zero new files, works everywhere, fits the server convention of injecting secrets via env.
**Cons**: Naming convention coupling. Doesn't cover `keychain.set()` or `keychain.list()`. Only solves read, not write.

### 4. Do nothing
Keychain is a desktop convenience. On servers, users should inject secrets via env vars and scripts should be written to check env first.

**Pros**: No code to write. No new attack surface.
**Cons**: `@keychain` is a first-class feature that silently doesn't work on a common deployment target.

## Recommendation

Probably option 2 (explicit opt-in file store) with option 3 (env var bridge for reads) as a complement. The file store covers the full API, the env bridge covers the 90% case with zero setup.

