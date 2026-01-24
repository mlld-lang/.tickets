---
id: mlld-hncp
status: closed
deps: [mlld-xbn2]
links: [mlld-dpgy]
created: 2026-01-15T11:03:44.843006-08:00
type: bug
priority: 0
parent: mlld-urik
---
# CRITICAL: /import policy does not register guards

## Problem

Only `/policy` directive registers guards. `/import policy` does NOT.

```mlld
// This registers guards:
/policy @p = { deny: { sh: true } }

// This does NOT register guards:
/import policy @p from "security-policy.mld"
// Policy config is imported but NOT ENFORCED at runtime
```

## Root Cause

From gpt52:
- `interpreter/eval/import/VariableImporter.ts:842` - imports policy but doesn't call guard generation
- `interpreter/eval/policy.ts:47` - only /policy directive generates and registers guards

## Current Flow

```
/policy @p = config
  → evaluatePolicy()
  → merges config into policySummary
  → generatePolicyGuards(merged)  ✅
  → registry.registerPolicyGuard() ✅

/import policy @p from "module"
  → importVariable()
  → sets @p to config value
  → NOTHING ELSE ❌
  → No guard generation ❌
```

## Required Fix

When importing policy, also generate and register guards:

```typescript
// VariableImporter.ts or ImportDirectiveEvaluator.ts
if (importKind === 'policy') {
  const policyConfig = importedValue;
  
  // Merge into policy summary
  env.recordPolicyConfig(alias, policyConfig);
  
  // Generate and register guards (same as /policy directive)
  const guards = generatePolicyGuards(policyConfig);
  const registry = env.getGuardRegistry();
  for (const guard of guards) {
    registry.registerPolicyGuard(guard);
  }
}
```

## Exit Criteria

- [ ] `/import policy @p from "module"` generates guards
- [ ] Imported policy guards are privileged (can't be bypassed)
- [ ] Policy deny rules from imports actually block operations
- [ ] Test verifies imported policy enforcement

## Estimated Effort
3-4 hours


