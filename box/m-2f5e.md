---
id: m-2f5e
status: closed
deps: [m-b4d2, m-86f5, m-04c2, m-3bea, m-5451, m-3742, m-4106, m-cdeb, m-d4e5]
links: []
created: 2026-03-04T01:40:25Z
type: epic
priority: 2
assignee: Adam
tags: [phase-10, policy, security]
updated: 2026-03-04T05:11:15Z
---
# Phase 10: Policy/Environment config alignment

Spec: `_specs/box.md` (Part 8: Policy/Environment Config Alignment)

Extend `PolicyConfig.env` to subsume environment constraints. Implement derivation function. Update evaluators to use policy-derived configs. Enable guards to return policy fragments.

## Goal
Make environment configuration fully expressible as policy, enabling a clear cascade:
```
Global Policy → Module Policy → Local Policy → Derived EnvConfig → Scoped Execution
```

## Cascade Rules (spec Part 8)
1. exe params > env config > policy > default (no restrictions)
2. Each level can only **attenuate** (restrict further), never expand
3. Policy enforcement ALWAYS applies (can't be superseded)
4. Policy can express all env config constraints declaratively

## Extend PolicyConfig (core/policy/union.ts)

Add `env` field to `PolicyConfig`:
```typescript
type PolicyConfig = {
  // ... existing policy fields ...

  env?: {
    // Provider policy
    default?: string;
    providers?: {
      [providerRef: string]: {
        allowed?: boolean;
        auth?: string | string[];
        taint?: DataLabel[];
        profiles?: Record<string, unknown>;
      };
    };

    // Tool policy
    tools?: {
      allow?: string[];
      deny?: string[];
      attenuation?: 'intersection' | 'union';  // default: intersection
    };

    // MCP server policy
    mcps?: {
      allow?: string[];
      deny?: string[];
    };

    // Network policy
    net?: {
      allow?: string[];
      deny?: string[];
    };
  };
}
```

## Derivation Function

```typescript
function deriveEnvironmentConfigFromPolicy(
  policy: PolicyConfig,
  localConfig?: EnvironmentConfig
): EnvironmentConfig {
  const derived: EnvironmentConfig = { ...localConfig };

  // Apply provider default
  if (!derived.provider && policy.env?.default) {
    derived.provider = policy.env.default;
  }

  // Enforce provider constraints
  const providerRules = policy.env?.providers?.[derived.provider ?? ''];
  if (providerRules?.allowed === false) {
    throw new MlldError(`Provider ${derived.provider} denied by policy`);
  }

  // Tool attenuation: intersection of policy allow + requested tools
  if (policy.env?.tools) {
    const policyAllowed = new Set(policy.env.tools.allow ?? []);
    const policyDenied = new Set(policy.env.tools.deny ?? []);
    const requestedTools = normalizeToolList(derived.tools);
    derived.tools = requestedTools.filter(t =>
      !policyDenied.has(t) &&
      (policyAllowed.size === 0 || policyAllowed.has(t))
    );
  }

  // MCP attenuation (analogous to tools)
  // ...

  return derived;
}
```

## Migration Path (spec Part 8)
1. Extend `PolicyConfig.env` structure, implement derivation function
2. Update `evaluateBox()` to use derived configs, update guard envConfig resolution
3. Allow `policy` directives to express environment constraints
4. Guards can return policy fragments that merge into active policy

## Files to Modify
- `core/policy/union.ts` — Extend PolicyConfig with `env` field
- `core/types/environment.ts` — Add `_policyDerivedConstraints` field
- `core/types/guard.ts` — Add policy fragment return type to GuardAction
- `interpreter/eval/policy.ts` — Support extended policy syntax
- `core/policy/guards.ts` — Generate guards from env policy
- `interpreter/env/environment-provider.ts` — Implement derivation, update `applyEnvironmentDefaults()`
- `interpreter/eval/box.ts` — Use derived configs in `evaluateBox()`
- `interpreter/hooks/guard-runtime-evaluator.ts` — Handle policy fragment returns from guards

## Acceptance Criteria
- `PolicyConfig.env` structure defined and typed
- Derivation function implemented and tested
- `evaluateBox()` uses policy-derived configs
- Tool attenuation works (intersection of policy + requested)
- Provider constraints enforced (denied providers throw)
- Guards can return policy fragments
- Policy cascade enforced (each level only attenuates)
- Unit tests for derivation function (all config combinations)
- Integration tests for policy → env config flow
- Guard policy fragment tests
- Docs: `POLICY.md` updated, security atoms updated
- All existing tests pass
- Committed
