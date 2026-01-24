---
id: mlld-7ud1
status: closed
deps: []
links: []
created: 2026-01-14T15:16:55.680832-08:00
type: task
priority: 0
parent: mlld-metu
---
# Phase 2.1: Fix /wants tier selection to respect policy [SECURITY-CRITICAL]

## Summary
Fix `selectWantsTier()` to check policy allow/deny rules instead of always granting the first matching tier.

## Security Impact
**CRITICAL**: Currently /wants ignores policy restrictions - a module declaring `wants: { sh }` always gets shell access even if policy denies it. This completely bypasses policy enforcement.

## Context
From audit-needs-wants.md: `selectWantsTier()` in `core/policy/needs.ts:216-226` accepts `PolicyCapabilities` (boolean flags) but ignores actual policy config with allow/deny rules.

### Current Broken Flow
```typescript
export function selectWantsTier(wants, policy: PolicyCapabilities) {
  // PolicyCapabilities is just boolean flags like { sh: true }
  // Ignores @mx.policy.configs.allow/deny
  // Always grants first tier where capabilities are available
}
```

### Required Fix
```typescript
export function selectWantsTier(wants, policyConfig: PolicyConfig) {
  // Check each tier against actual policy rules
  for (const tier of wants.tiers) {
    if (policyPermitsCapabilities(tier.needs, policyConfig)) {
      return tier.name;
    }
  }
  return null; // No tier permitted
}

function policyPermitsCapabilities(tierNeeds, policy) {
  // Check tier commands against policy.allow.cmd / policy.deny.cmd
  // Check tier capabilities against policy allow/deny
  // Return false if policy denies anything tier requires
}
```

## Implementation

### Files to Modify

1. **`core/policy/needs.ts:216-226`** - Main fix
   - Change `selectWantsTier()` signature to accept full `PolicyConfig`
   - Implement `policyPermitsCapabilities()` helper
   - Check:
     - `tier.needs.cmd` against `policy.allow.cmd` and `policy.deny.cmd`
     - `tier.needs.sh` against `policy.allow.sh` and `policy.deny.sh`
     - Other capabilities similarly

2. **`interpreter/eval/needs.ts:28-56`** - Call site update
   - Pass full policy config instead of PolicyCapabilities
   - May need to get policy from Environment differently

3. **`interpreter/env/Environment.ts:858-866`** - May need changes
   - `getPolicyCapabilities()` may need to return more info
   - Or create new method to get full policy config

## Exit Criteria
- [ ] Tier rejected when policy denies required capability
- [ ] Tier rejected when policy denies required command
- [ ] Most permissive allowed tier is selected (not just first declared)
- [ ] @mx.policy.tier reflects actual policy decision
- [ ] All existing needs/wants tests pass
- [ ] Build succeeds with no type errors

## Tests to Add
Create `tests/cases/security/wants-respects-policy/`
```
example.md:
  /policy {
    deny: { sh: true }
  }
  /wants [
    { tier: "full", sh },
    { tier: "readonly" }
  ]
  /show @mx.policy.tier
expected.md:
  readonly
```

Create `tests/cases/security/wants-denies-all-tiers/`
```
example.md:
  /policy {
    deny: { network: true }
  }
  /needs { network }
  /wants [
    { tier: "full", network },
    { tier: "limited", network }
  ]
error.md:
  No permitted tier found
```

## Dependencies
None - critical path item

## Estimated Effort
4 hours


