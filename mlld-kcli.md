---
id: mlld-kcli
status: closed
deps: [mlld-kinit]
links: []
created: 2026-01-20T08:37:12.083895-08:00
type: feature
priority: 1
---
# Keychain CLI commands (add/rm/list/get/import)

## Summary

Add CLI commands for managing keychain credentials.

## Commands

```bash
mlld keychain add <name>              # Prompts for value (secure)
mlld keychain add <name> --value <v>  # Explicit value
mlld keychain rm <name>               # Remove credential
mlld keychain list                    # List names (not values)
mlld keychain get <name>              # Output value (for scripting)
mlld keychain import <file.env>       # Import from .env file
```

## Namespace

All operations scoped to `mlld-env-{projectname}` service in system keychain.

- Project name from `mlld-config.json` (see mlld-kinit)
- Example: `mlld keychain add claude` → `(mlld-env-myproject, claude)`

## Import Format

Standard `.env` format:
```
ANTHROPIC_API_KEY=sk-...
GH_TOKEN=ghp_...
```

Maps to:
- `mlld-env-{project}/ANTHROPIC_API_KEY`
- `mlld-env-{project}/GH_TOKEN`

## Implementation

1. Add `cli/commands/keychain.ts`
2. Use existing `KeychainProvider` from `core/resolvers/builtin/KeychainResolver.ts`
3. Read project name from mlld-config.json
4. Register in CommandDispatcher

## Exit Criteria

- [ ] `mlld keychain add` works with prompt
- [ ] `mlld keychain rm` removes credentials
- [ ] `mlld keychain list` shows names
- [ ] `mlld keychain get` outputs value
- [ ] `mlld keychain import` parses .env files
- [ ] All operations scoped to project namespace
- [ ] Tests for CLI commands


