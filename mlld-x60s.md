---
id: mlld-x60s
status: closed
deps: []
links: []
created: 2026-01-14T16:02:15.157597-08:00
type: task
priority: 1
parent: mlld-qs0y
---
# Workstream D: Keychain & Capabilities

## Workstream Overview

This workstream implements the keychain capability for secure credential storage and the full environment module CLI.

**Parent Epic**: mlld-qs0y (MCP, Environment Modules, and Policy Integration)

## Why This Matters

Environment modules need:
- Secure credential storage (not plaintext files)
- Automatic `secret` labeling for tokens
- `/needs` validation at load time
- CLI commands to capture/spawn/list environments

## Sequence

```
mlld-ov8w (2.2 Keychain grammar) [2h]
    │
    ▼
mlld-8ohj (2.3 Needs validation) [3h] 🔒
    │
    ▼
mlld-esjy (4.1 Keychain builtins) [2h]
    │
    ▼
mlld-ut9i (4.2 macOS keychain) [3h]
    │
    ├──→ mlld-bniq (5.1 Env module type) [0.5h]
    │
    └──→ [Joins with Workstream A for Phase 6.x]
         │
         ▼
    mlld-19ya (6.1 List) [2h]
    mlld-l6n4 (6.2 Capture) [4h]
    mlld-hmw5 (6.3 Spawn) [4h]
    mlld-9rot (6.4 Shell) [2h]
```

## Beads in This Workstream

### Phase 2: Capabilities
1. **mlld-ov8w** - Phase 2.2: Add keychain capability to grammar
2. **mlld-8ohj** - Phase 2.3: Validate /needs at module load time [SECURITY]

### Phase 4: Keychain Implementation
3. **mlld-esjy** - Phase 4.1: Keychain built-in functions
4. **mlld-ut9i** - Phase 4.2: macOS keychain implementation

### Phase 5: Module Type
5. **mlld-bniq** - Phase 5.1: Add environment module type

### Phase 6: CLI Commands (after joining with Workstream A)
6. **mlld-19ya** - Phase 6.1: mlld env list
7. **mlld-l6n4** - Phase 6.2: mlld env capture  
8. **mlld-hmw5** - Phase 6.3: mlld env spawn
9. **mlld-9rot** - Phase 6.4: mlld env shell

## Key Files

```
grammar/directives/needs-wants.peggy:183-189 (keychain capability)
core/policy/needs.ts (capability validation)
interpreter/eval/needs.ts (load-time validation)
interpreter/builtins/keychain.ts (built-in functions)
interpreter/builtins/keychain-macos.ts (macOS impl)
core/registry/types.ts (environment module type)
cli/commands/env/*.ts (CLI commands)
```

## Keychain API

```mlld
/needs { keychain }

/var secret @token = keychain.get("mlld-env", "my-env")
keychain.set("mlld-env", "my-env", @newToken)
keychain.delete("mlld-env", "my-env")
```

Values from `keychain.get()` automatically get `secret` label.

## Context Documents

- `todo/audits/audit-needs-wants.md` - Needs validation gaps
- `todo/audits/audit-cli-env.md` - CLI structure
- `todo/spec-mcp-env-policy.md` Part 3 - Environment modules

## Exit Criteria

- [ ] `/needs { keychain }` parses
- [ ] Modules fail fast if keychain unavailable
- [ ] `keychain.get()` returns values with `secret` label
- [ ] macOS keychain operations work via `security` command
- [ ] `type: environment` recognized in module.yml
- [ ] `mlld env list/capture/spawn/shell` all functional
- [ ] Token stored securely, not in plaintext files

## Estimated Effort
22.5 hours total

## Platform Note

v1 is macOS only for keychain. Linux Secret Service support planned for v1.1.

## Tests to Add

```
tests/cases/slash/needs/keychain-capability/
tests/cases/exceptions/needs-unmet-keychain/
tests/cases/security/keychain-needs-required/
tests/cases/security/keychain-secret-label/
```


