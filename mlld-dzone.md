---
id: mlld-dzone
status: closed
deps: [mlld-ppath]
links: []
created: 2026-01-20T08:37:12.355399-08:00
type: feature
priority: 1
---
# Danger list in policy capabilities (allow.danger)

## Summary

Add `danger` list to policy `allow` for dangerous operations that must be explicitly enabled.

## Syntax

```mlld
policy {
  capabilities: {
    allow: [
      "cmd:git:*",
      "cmd:npm:*",
      "fs:r:**"
    ],
    
    danger: [
      "@keychain",
      "fs:r:~/.ssh/*"
    ]
  }
}
```

## Behavior

These items are **default deny** via internal policy. Adding to regular `allow` is not enough - they must be in `allow.danger`.

## Default Danger List (all denied unless explicitly enabled)

### Credential Access
- `@keychain` - Direct keychain access in scripts
- `cmd:mlld:keychain:*` - Keychain CLI from scripts
- `cmd:security:*` - macOS security command
- `cmd:pass:*` - pass password manager
- `cmd:op:*` - 1Password CLI

### Credential File Paths
- `fs:r:~/.ssh/*` - SSH keys
- `fs:r:~/.aws/*` - AWS credentials
- `fs:r:~/.config/gh/*` - GitHub CLI tokens
- `fs:r:~/.netrc` - Network credentials
- `fs:r:**/.env` - Environment files
- `fs:r:**/*.pem` - Certificates/keys
- `fs:r:**/*_rsa` - SSH private keys
- `fs:r:**/*_ed25519` - SSH private keys

### Agent/LLM Configs
- `fs:w:.claude/*` - Claude Code config
- `fs:w:.codex/*` - Codex config
- `fs:w:.mlld/*` - mlld config
- `fs:w:.cursor/*` - Cursor config
- `fs:w:.continue/*` - Continue config
- `fs:r:**/.anthropic/*` - Anthropic SDK config
- `fs:r:**/.openai/*` - OpenAI config

### Safety Bypasses
- `cmd:claude:*:--dangerously-skip-permissions`
- `cmd:codex:*:--dangerously-bypass-approvals-and-sandbox`
- `cmd:codex:*:--full-auto`
- `cmd:mlld:*:--no-policy`
- `cmd:mlld:*:--no-guards`

### Destructive System Commands
- `cmd:rm:*:-rf` - Recursive force delete
- `cmd:sudo:*` - Privilege escalation
- `cmd:chmod:*` - Permission changes
- `cmd:chown:*` - Ownership changes
- `cmd:dd:*` - Raw disk access
- `cmd:mkfs:*` - Filesystem creation

### Git Exfiltration/Destruction
- `cmd:git:push:*:--force` - Force push
- `cmd:git:remote:add:*` - Adding arbitrary remotes

### Process Manipulation
- `cmd:kill:*`
- `cmd:pkill:*`
- `cmd:killall:*`

### Network Exfil Commands
- `cmd:curl:*:--upload-file`
- `cmd:curl:*:-T`
- `cmd:scp:*`
- `cmd:rsync:*`
- `cmd:nc:*`

## Implementation

1. Define internal danger list in `core/policy/danger.ts`
2. Capability enforcement checks danger list before regular allow
3. If item is in danger list and NOT in `allow.danger`, deny
4. Clear denial message: "Dangerous capability requires allow.danger"

## Exit Criteria

- [ ] Danger list defined with all items above
- [ ] `allow.danger` parsed in policy
- [ ] Dangerous items denied even if in regular `allow`
- [ ] Only `allow.danger` enables dangerous items
- [ ] KeychainResolver checks danger policy
- [ ] Uses `fs:` pattern syntax (see mlld-ppath)
- [ ] Clear denial messages explain how to enable
- [ ] Tests for each category


