---
id: mlld-14od
status: closed
deps: [mlld-7ud1]
links: []
created: 2026-01-14T15:17:57.584143-08:00
type: task
priority: 0
parent: mlld-metu
---
# Phase 2.5: Policy guard generation + privileged guards [SECURITY-CRITICAL]

## Summary
Generate guard definitions from merged policy config and make them privileged (cannot be bypassed with `with { guards: false }`).

## Security Impact
**CRITICAL**: Policy config is currently data only - it records allow/deny but doesn't actually block anything. This phase activates actual enforcement.

## Context
From audit-policy.md: `interpreter/eval/policy.ts:44` records policy but doesn't generate guards. Policy enforcement requires:
1. Generating guards from policy allow/deny rules
2. Marking these guards as privileged (ignore bypass requests)
3. Activating guards when `/policy` or `/import policy` evaluates

## Implementation

### Part A: Generate Guards from Policy

Create `core/policy/guards.ts`:
```typescript
export function generatePolicyGuards(policy: PolicyConfig): GuardDefinition[] {
  const guards: GuardDefinition[] = [];
  
  // Deny shell access
  if (policy.deny?.sh) {
    guards.push({
      name: '@__policy_deny_sh',
      filter: 'before op:run',
      privileged: true,
      handler: (ctx) => {
        const cmd = ctx.op.metadata?.command?.split(' ')[0];
        if (cmd === 'sh' || cmd === 'bash') {
          return { action: 'deny', reason: 'Shell access denied by policy' };
        }
        return { action: 'allow' };
      }
    });
  }
  
  // Deny specific commands
  if (policy.deny?.cmd) {
    guards.push({
      name: '@__policy_deny_cmd',
      filter: 'before op:run',
      privileged: true,
      handler: (ctx) => {
        const cmd = ctx.op.metadata?.command?.split(' ')[0];
        if (policy.deny.cmd.includes(cmd)) {
          return { action: 'deny', reason: `Command '${cmd}' denied by policy` };
        }
        return { action: 'allow' };
      }
    });
  }
  
  // Network blocking
  if (policy.deny?.network) {
    guards.push({
      name: '@__policy_deny_network',
      filter: 'before net:*',
      privileged: true,
      handler: () => ({ action: 'deny', reason: 'Network access denied by policy' })
    });
  }
  
  // ... similar for other capabilities
  
  return guards;
}
```

### Part B: Add Privileged Flag to Guards

1. **`core/types/guard.ts`**
   ```typescript
   interface GuardDefinition {
     name: string;
     filter: string;
     handler: GuardHandler;
     privileged?: boolean;  // ADD THIS
   }
   ```

2. **`interpreter/hooks/guard-pre-hook.ts`**
   Around line 438-442 where bypass is checked:
   ```typescript
   // Before applying override
   if (override.guards === false || override.guards?.except) {
     // Filter out guards, but KEEP privileged ones
     activeGuards = activeGuards.filter(g => 
       g.privileged || !shouldBypass(g, override)
     );
   }
   ```

### Part C: Register Guards on Policy Evaluation

1. **`interpreter/eval/policy.ts`**
   After recording policy config:
   ```typescript
   // Generate and register guards
   const policyGuards = generatePolicyGuards(mergedPolicy);
   for (const guard of policyGuards) {
     env.registerGuard(guard);
   }
   ```

2. **`interpreter/eval/import/ImportDirectiveEvaluator.ts`**
   For `/import policy @name from "module"`:
   - Load the policy module
   - Generate guards from imported policy
   - Register as privileged

### Files to Modify
- `core/policy/guards.ts` (new)
- `core/types/guard.ts`
- `interpreter/eval/policy.ts`
- `interpreter/eval/import/ImportDirectiveEvaluator.ts`
- `interpreter/hooks/guard-pre-hook.ts`

## Exit Criteria
- [ ] `/policy { deny: { sh } }` blocks shell commands
- [ ] `/policy { deny: { cmd: [curl] } }` blocks specific commands
- [ ] Policy guards fire even with `with { guards: false }`
- [ ] `/import policy @p from "module"` activates guards from imported policy
- [ ] Error messages clearly state "denied by policy"
- [ ] All existing tests pass
- [ ] Build succeeds

## Tests to Add

Create `tests/cases/security/policy-denies-shell/`
```
example.md:
  /policy { deny: { sh: true } }
  /run sh -c "echo hello"
error.md:
  Shell access denied by policy
```

Create `tests/cases/security/policy-guards-privileged/`
```
example.md:
  /policy { deny: { cmd: [cat] } }
  /exe @readFile(path) = run cat @path
  /var @content = @readFile("test.txt") with { guards: false }
error.md:
  Command 'cat' denied by policy
```

Create `tests/cases/security/import-policy-activates/`
(test that /import policy registers guards)

## Dependencies
- Phase 2.1 (tier selection respects policy) - logical dependency
- Phase 1.2 (taint exposure) - for guards that check taint

## Estimated Effort
8 hours


