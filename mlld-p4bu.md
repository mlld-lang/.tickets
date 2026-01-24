---
id: mlld-p4bu
status: closed
deps: []
links: []
created: 2026-01-14T20:03:03.858026-08:00
type: bug
priority: 1
parent: mlld-2ubc
---
# B2: Guard bypass config must block { only: ... } [HIGH]

## Problem

When `security.allowGuardBypass: false`, the code blocks:
- `with { guards: false }` 
- `with { guards: { except: [@guard] } }`

But still allows:
- `with { guards: { only: [@guard] } }` ← GAP

If "no bypass" means no bypass, this is a security hole.

## Current Code (guard-pre-hook.ts:445-452)

```typescript
if (guardOverride.kind === 'disableAll' || guardOverride.kind === 'except') {
  const projectConfig = env.getProjectConfig();
  if (projectConfig && !projectConfig.getAllowGuardBypass()) {
    throw new MlldSecurityError(
      'Guard bypass disabled by security config...'
    );
  }
}
// 'only' kind not checked!
```

## Required Fix

```typescript
if (guardOverride.kind === 'disableAll' || 
    guardOverride.kind === 'except' || 
    guardOverride.kind === 'only') {  // ADD THIS
  const projectConfig = env.getProjectConfig();
  if (projectConfig && !projectConfig.getAllowGuardBypass()) {
    throw new MlldSecurityError(
      'Guard bypass disabled by security config - guards cannot be skipped in this environment',
      { code: 'GUARD_BYPASS_BLOCKED' }
    );
  }
}
```

## Files to Modify

1. `interpreter/hooks/guard-pre-hook.ts:445`
   - Add `guardOverride.kind === 'only'` to condition

## Test Case

```typescript
// tests/integration/guard-bypass-config.test.ts
it('blocks guards: { only } when allowGuardBypass is false', async () => {
  // Config: security.allowGuardBypass: false
  // Attempt: with { guards: { only: [@someGuard] } }
  // Expected: MlldSecurityError
});
```

## Exit Criteria

- [ ] `guards: { only: [...] }` blocked when allowGuardBypass=false
- [ ] Error message matches other bypass blocking
- [ ] Test added to guard-bypass-config.test.ts
- [ ] Existing tests still pass

## Estimated Effort
1 hour


