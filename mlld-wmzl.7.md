---
id: mlld-wmzl.7
status: closed
deps: [mlld-wmzl.4]
links: []
created: 2026-01-16T07:19:32.107616-08:00
type: task
priority: 1
parent: mlld-wmzl
---
# Phase 7: /profiles directive (replace /wants)

## Goal

Replace `/wants` with `/profiles` per spec. Profiles define tiered capability bundles for graceful degradation.

## Difference from /wants

- `/wants` was a simple tier list
- `/profiles` is named tiers with explicit requirements and descriptions

## Syntax

```mlld
/profiles {
  full: {
    requires: { sh, network },
    description: "Full development access"
  },
  network: {
    requires: { network },
    description: "Network operations without shell"
  },
  readonly: {
    requires: { },
    description: "Read-only local operations"
  }
}
```

## Profile Selection

1. System infers what capabilities the module uses (AST analysis)
2. Evaluates profiles against policy in declaration order
3. First profile where all `requires` are permitted by policy is selected
4. Selected profile available as `@mx.profile`

```mlld
when @mx.profile == "full" => // use network + shell features
when @mx.profile == "readonly" => // use local-only fallback
```

## Grammar Update

**File**: `grammar/directives/needs-wants.peggy`

```pegjs
ProfilesKeyword = "/"? "profiles"

SlashProfiles
  = DirectiveContext ProfilesKeyword _ profiles:ProfilesObject ... {
      return helpers.createStructuredDirective(
        DirectiveKind.profiles,
        'profiles',
        { profiles },
        ...
      );
    }

ProfilesObject
  = "{" _ entries:ProfileEntryList? _ "}" { ... }

ProfileEntry
  = name:Identifier _ ":" _ "{" _ 
    requires:ProfileRequires? _ 
    description:ProfileDescription? _ 
    "}" { ... }
```

## Evaluator

**File**: `interpreter/eval/profiles.ts` (NEW or rename from wants.ts)

```typescript
export async function evaluateProfiles(node: any, env: Environment): Promise<void> {
  const profiles = node.values?.profiles;
  const policy = env.getPolicySummary();

  for (const [name, profile] of Object.entries(profiles)) {
    if (profileRequirementsMet(profile.requires, policy)) {
      env.setVariable('@mx.profile', name);
      return;
    }
  }

  // Fallback to last profile (minimal)
  const fallback = Object.keys(profiles).pop();
  env.setVariable('@mx.profile', fallback);
}

function profileRequirementsMet(
  requires: Record<string, any>,
  policy: PolicySummary
): boolean {
  // Check each required capability against policy
  for (const [cap, value] of Object.entries(requires)) {
    if (!policy.allows(cap, value)) {
      return false;
    }
  }
  return true;
}
```

## Files to Modify

| File | Change |
|------|--------|
| `grammar/directives/needs-wants.peggy` | Add /profiles grammar |
| `interpreter/eval/profiles.ts` | NEW - evaluation logic |
| `core/types/primitives.ts` | Add DirectiveKind.profiles |
| Existing /wants code | Deprecate or remove |

## Exit Criteria

- [ ] `/profiles` grammar parses correctly
- [ ] Profile selection evaluates in order
- [ ] First matching profile is selected
- [ ] `@mx.profile` variable set correctly
- [ ] Fallback to last profile works
- [ ] Tests verify profile selection logic

## References

- Spec Section 2.5: Profiles
- Plan Phase 7: `todo/plan-security-2026-v3.md` lines 618-670

## Estimated Effort
~4 hours


