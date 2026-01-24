---
id: mlld-e817
status: closed
deps: [mlld-14od]
links: []
created: 2026-01-14T15:18:50.826523-08:00
type: task
priority: 0
parent: mlld-metu
---
# Phase 9: Guard bypass configuration [SECURITY]

## Summary
Add config option `security.allowGuardBypass` to disable `with { guards: false }` bypass at the project level.

## Security Impact
Production environments should be able to prevent ALL guard bypass, not just policy guards. This is the final safety mechanism.

## Context
Phase 2.5 makes policy guards privileged (always enforce). But non-policy guards can still be bypassed with `with { guards: false }`. Production deployments need to disable this entirely.

## Implementation

### 1. Add Config Schema

Location: `core/config/schema.ts` (or wherever mlld-config.json schema is defined)

```typescript
interface MlldConfig {
  // ...existing fields
  security?: {
    allowGuardBypass?: boolean;  // default: true for backwards compat
  };
}
```

### 2. Check Config Before Bypass

Location: `interpreter/hooks/guard-pre-hook.ts:438-442`

```typescript
// Before applying override
if (override.guards === false || override.guards?.except) {
  const config = env.getConfig();
  
  if (config.security?.allowGuardBypass === false) {
    throw new MlldSecurityError(
      'Guard bypass disabled by security config - guards cannot be skipped in this environment'
    );
  }
  
  // If allowed, filter out non-privileged guards (existing logic)
  activeGuards = activeGuards.filter(g => 
    g.privileged || !shouldBypass(g, override)
  );
}
```

### 3. Create Error Type

```typescript
export class MlldSecurityError extends MlldError {
  constructor(message: string) {
    super(message, 'SECURITY_VIOLATION');
  }
}
```

### 4. Document in Security Docs

Add to `docs/user/security.md`:
```markdown
## Production Configuration

For production environments, disable guard bypass:

\`\`\`json
// mlld-config.json
{
  "security": {
    "allowGuardBypass": false
  }
}
\`\`\`

With this setting, \`with { guards: false }\` will throw an error
instead of bypassing guards.
\`\`\`

## Files to Modify
- `core/config/schema.ts` - Add security.allowGuardBypass
- `interpreter/hooks/guard-pre-hook.ts:438-442` - Check config
- `core/errors/` - Add MlldSecurityError if not exists
- `docs/user/security.md` - Document production config

## Exit Criteria
- [ ] `security.allowGuardBypass: false` in config blocks `with { guards: false }`
- [ ] Default is `true` (backwards compatible)
- [ ] Error message is clear and actionable
- [ ] `with { guards: { except: [...] } }` is also blocked when config is false
- [ ] Policy guards still work regardless of config setting
- [ ] All existing tests pass
- [ ] Documentation explains production setup

## Tests to Add

Create `tests/cases/security/guard-bypass-blocked/`
```
example.md:
  >> With config security.allowGuardBypass: false
  /guard @blocker before show = deny "Blocked"
  /show "test" with { guards: false }
error.md:
  Guard bypass disabled by security config
```
Note: Need way to set config for test, or mock config.

Create `tests/cases/security/guard-bypass-allowed/`
```
example.md:
  >> With config security.allowGuardBypass: true (default)
  /guard @blocker before show = deny "Blocked"
  /show "test" with { guards: false }
expected.md:
  test
```

## Dependencies
- Phase 2.5 (privileged guards) - establishes guard override mechanism

## Estimated Effort
2 hours


