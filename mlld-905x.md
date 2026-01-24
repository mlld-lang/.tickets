---
id: mlld-905x
status: closed
deps: []
links: []
created: 2026-01-15T11:03:31.273791-08:00
type: bug
priority: 0
parent: mlld-urik
---
# CRITICAL: /wants ignores policy allow constraints

## Problem

/wants tier selection ignores policy ALLOW constraints - only checks DENY.

- `getPolicyCapabilities()` defaults to `ALLOW_ALL_POLICY` 
- Never derived from actual policy config
- `policyConfigPermitsTier()` only checks `deny`, not `allow`
- Result: Tiers can grant capabilities beyond what policy explicitly allows

## Example Attack

```mlld
/policy { 
  allow: { cmd: [git] }  // Only git allowed
}

/wants [
  { tier: "full", sh },  // Requests shell access
  { tier: "limited" }
]
// Full tier GRANTED even though policy only allows git!
```

## Root Cause

From gpt52:
- `interpreter/eval/needs.ts:77` - reads getPolicyCapabilities
- `interpreter/env/Environment.ts:133` - ALLOW_ALL_POLICY default
- `interpreter/env/Environment.ts:910` - getPolicyCapabilities never uses policySummary
- `core/policy/needs.ts:239` - policyConfigPermitsTier only checks deny

## Required Fix

### Option A: Derive capabilities from policy

```typescript
// Environment.ts
getPolicyCapabilities(): PolicyCapabilities {
  const policy = this.getPolicySummary();
  if (!policy) return ALLOW_ALL_POLICY;
  
  return {
    sh: !policy.deny?.sh && (policy.allow?.sh !== false),
    network: !policy.deny?.network && (policy.allow?.network !== false),
    // ...derive from actual policy
  };
}
```

### Option B: Check allow in policyConfigPermitsTier

```typescript
// core/policy/needs.ts
function policyConfigPermitsTier(tierNeeds, policyConfig): boolean {
  // Existing deny checks...
  
  // ADD: Check allow constraints
  if (policyConfig.allow) {
    if (tierNeeds.sh && !isAllowed('sh', policyConfig.allow)) return false;
    if (tierNeeds.cmd && !commandsAllowed(tierNeeds.cmd, policyConfig.allow.cmd)) return false;
    // ...
  }
  
  return true;
}
```

## Exit Criteria

- [ ] Policy with `allow: { cmd: [git] }` blocks tiers requiring `sh`
- [ ] Policy with `allow: { sh: false }` blocks tiers requiring sh
- [ ] getPolicyCapabilities reflects actual policy, not ALLOW_ALL
- [ ] Tests verify allow constraints are enforced

## Estimated Effort
4-6 hours


