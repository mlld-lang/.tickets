---
id: mlld-wmzl.10
status: closed
deps: [mlld-wmzl.4]
links: []
created: 2026-01-16T07:49:45.412129-08:00
type: task
priority: 1
parent: mlld-wmzl
---
# Phase 4.5: Guard bypass syntax (with { guards: ... })

## Goal

Implement guard bypass syntax per spec section 4.7. This allows specific operations to bypass guards when needed.

## Syntax

```mlld
run { echo "test" } with { guards: false }                    // Bypass all
run { echo "test" } with { guards: { only: [@specific] } }    // Only these
run { echo "test" } with { guards: { except: [@noisy] } }     // All except these
```

## Important Constraints

1. **Policy-generated guards CANNOT be bypassed** - guards from \`policy { ... }\` are always enforced
2. **Privileged imported guards CANNOT be bypassed** - guards from \`import guards privileged from "..."\`
3. Only user-defined, non-privileged guards can be bypassed

## Implementation

### 1. Grammar Update

**File**: Likely in \`grammar/directives/run.peggy\` or \`grammar/common/with-clause.peggy\`

The \`with\` clause needs to accept a \`guards\` option:

```pegjs
WithGuards
  = "guards" _ ":" _ value:GuardBypassValue { return { guards: value }; }

GuardBypassValue
  = "false" { return false; }
  / "{" _ "only" _ ":" _ guards:GuardList _ "}" { return { only: guards }; }
  / "{" _ "except" _ ":" _ guards:GuardList _ "}" { return { except: guards }; }
```

### 2. Evaluator Update

**File**: \`interpreter/eval/run.ts\` and other evaluators that support \`with\` clause

```typescript
function shouldRunGuard(
  guard: Guard,
  bypassConfig: GuardBypassConfig | undefined
): boolean {
  // Policy-generated guards always run
  if (guard.isPrivileged) {
    return true;
  }
  
  if (bypassConfig === false) {
    return false;  // Bypass all non-privileged
  }
  
  if (bypassConfig?.only) {
    return bypassConfig.only.includes(guard.name);
  }
  
  if (bypassConfig?.except) {
    return !bypassConfig.except.includes(guard.name);
  }
  
  return true;  // Default: run all guards
}
```

### 3. Guard Privilege Tracking

Ensure guards track their privilege status:

```typescript
interface Guard {
  name: string;
  // ...
  isPrivileged: boolean;  // true if from policy or privileged import
}
```

## Files to Modify

| File | Change |
|------|--------|
| Grammar for with-clause | Add \`guards\` option |
| \`interpreter/eval/run.ts\` | Check bypass config |
| \`interpreter/hooks/guard-pre-hook.ts\` | Filter guards by bypass config |
| Guard registration | Track privilege status |

## Exit Criteria

- [ ] \`with { guards: false }\` bypasses non-privileged guards
- [ ] \`with { guards: { only: [...] } }\` runs only specified guards
- [ ] \`with { guards: { except: [...] } }\` runs all except specified
- [ ] Policy-generated guards still run with \`guards: false\`
- [ ] Privileged imported guards still run with \`guards: false\`
- [ ] Tests verify bypass works
- [ ] Tests verify privileged guards can't be bypassed

## References

- Spec Section 4.7: Guard Bypass

## Estimated Effort
~3 hours


