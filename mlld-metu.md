---
id: mlld-metu
status: closed
deps: []
links: []
created: 2026-01-14T16:01:49.100273-08:00
type: task
priority: 0
parent: mlld-qs0y
---
# Workstream C: Policy Enforcement [SECURITY-CRITICAL]

## Workstream Overview

This workstream implements policy-based capability restrictions and privileged guard enforcement.

**Parent Epic**: mlld-qs0y (MCP, Environment Modules, and Policy Integration)

## Why This Matters

Currently:
- `/wants` ignores policy allow/deny - always grants first tier
- `/policy` records config but doesn't generate guards
- Guards can be bypassed with `with { guards: false }`

After this workstream:
- Tier selection respects policy restrictions
- Policy generates privileged guards that enforce rules
- Production can disable all guard bypass

## Sequence

```
mlld-7ud1 (2.1 Fix tier selection) [4h] 🔒
    │
    ▼
mlld-14od (2.5 Policy guards + privileged) [8h] 🔒
    │
    ▼
mlld-e817 (9 Guard bypass config) [2h] 🔒
```

## Beads in This Workstream

1. **mlld-7ud1** - Phase 2.1: Fix /wants tier selection [SECURITY-CRITICAL]
   - Change `selectWantsTier()` to accept full PolicyConfig
   - Implement `policyPermitsCapabilities()` checker
   - Reject tiers when policy denies required capabilities

2. **mlld-14od** - Phase 2.5: Policy guard generation [SECURITY-CRITICAL]
   - Generate guards from policy allow/deny rules
   - Add `privileged` flag to GuardDefinition
   - Privileged guards ignore `with { guards: false }`
   - Activate guards on `/policy` and `/import policy`

3. **mlld-e817** - Phase 9: Guard bypass configuration [SECURITY]
   - Add `security.allowGuardBypass` config option
   - When false, `with { guards: false }` throws error
   - Default true for backwards compatibility

## Key Files

```
core/policy/needs.ts:216-226 (tier selection)
core/policy/guards.ts (new - guard generation)
interpreter/eval/policy.ts (guard registration)
interpreter/hooks/guard-pre-hook.ts:438-442 (bypass check)
core/types/guard.ts (privileged flag)
```

## Policy Guard Mapping

| Policy Rule | Generated Guard |
|-------------|-----------------|
| `deny: { sh }` | Block `sh`/`bash` in op:run |
| `deny: { cmd: [curl] }` | Block specific commands |
| `deny: { network }` | Block ops with `net:*` labels |
| `allow: { cmd: [git] }` | Only allow listed commands |

## Context Documents

- `todo/audits/audit-policy.md` - Policy system gaps
- `todo/audits/audit-needs-wants.md` - Tier selection bug
- `todo/spec-mcp-env-policy.md` Part 4 - Policy integration

## Exit Criteria

- [ ] Tier rejected when policy denies required capability
- [ ] `/policy { deny: { sh } }` actually blocks shell
- [ ] Policy guards fire even with `with { guards: false }`
- [ ] `security.allowGuardBypass: false` blocks all bypass
- [ ] Error messages clearly state "denied by policy"
- [ ] Tests verify enforcement

## Estimated Effort
14 hours total (4h + 8h + 2h)

## Tests to Add

```
tests/cases/security/wants-respects-policy/
tests/cases/security/policy-denies-shell/
tests/cases/security/policy-guards-privileged/
tests/cases/security/guard-bypass-blocked/
```


