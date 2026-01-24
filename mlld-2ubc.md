---
id: mlld-2ubc
status: closed
deps: []
links: []
created: 2026-01-14T20:02:27.400442-08:00
type: task
priority: 1
parent: mlld-vr3w
---
# Track B: Contract Compliance [HIGH]

## Track Overview

Fix spec violations where implementation diverges from the designed behavior.

**Parent Epic**: mlld-vr3w

## Issues in This Track

### B1: env spawn/shell must load environment module [HIGH]
- Currently bypasses module load, /wants, @mcpConfig, exported functions
- Should evaluate module, resolve tier, call @spawn/@shell exports

### B2: Guard bypass config must block { only: ... } [HIGH]  
- `allowGuardBypass: false` blocks `guards: false` and `except`
- But still allows `guards: { only: [@guard] }` to selectively disable
- Should block all override forms when config is false

### B3: Validate environment name (path traversal) [MEDIUM]
- `mlld env capture ../x` can write outside .mlld/env/
- Need input validation on environment names

### B4: Validate module type in findEnvModule [MEDIUM]
- Only checks module.yml exists, not `type: environment`
- Non-environment modules incorrectly treated as environments

### B5: Add platform guard for keychain CLI [MEDIUM]
- Uses MacOSKeychainProvider directly without platform check
- Non-macOS errors are opaque spawn failures

## Sequence

```
B1 (env contract) ─────────────────┐
                                   │
B2 (guard bypass) ─────────────────┤
                                   ├──→ Contract compliant
B3 (path traversal) ───────────────┤
                                   │
B4 (module type) ──────────────────┤
                                   │
B5 (platform guard) ───────────────┘
```

All items can run in parallel.

## Key Files

```
cli/commands/env.ts:19,143,189,223,253,281,309
interpreter/hooks/guard-pre-hook.ts:445
```

## Exit Criteria

- [ ] `mlld env spawn` evaluates environment module fully
- [ ] `mlld env shell` evaluates environment module fully
- [ ] `guards: { only: [...] }` blocked when allowGuardBypass=false
- [ ] Path traversal in env names rejected
- [ ] Non-environment modules rejected by findEnvModule
- [ ] Clear error on non-macOS keychain usage

## Estimated Effort
12-17 hours total


