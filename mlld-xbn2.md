---
id: mlld-xbn2
status: deferred
deps: []
links: []
created: 2026-01-19T22:41:51.961698-08:00
type: task
priority: 3
---
# Policy merge/composition (selective imports, cascade)

## Summary

Implement policy merge semantics from spec v4 Section 8.1.

## Grammar Changes

### Selective Import Syntax

```pegjs
PolicyImport
  = "import" _ "{" _ fields:PolicyFieldList _ "}" _ "from" _ source:ModuleRef
  / "import" _ "policy" _ "from" _ source:ModuleRef _ except:ExceptClause?

PolicyFieldList
  = head:PolicyField tail:(_ "," _ PolicyField)*

PolicyField
  = path:DottedPath  // e.g., "allow.cmd", "sources", "defaults.rules"

DottedPath
  = head:Identifier tail:("." Identifier)*

ExceptClause
  = "except" _ "{" _ fields:PolicyFieldList _ "}"
```

### Examples

```mlld
import { allow.cmd } from "@company/security"
import { sources, operations } from "./policies/base.mld"
import policy from "@mlld/baseline" except { deny }
```

## Merge Logic (core/policy/union.ts)

### Current State

`mergePolicyConfigs()` already exists with:
- `allow` intersection
- `deny` union
- `limits` minimum
- `default` most restrictive

### New Requirements

1. **Selective field extraction** before merge
2. **Except clause** - exclude fields before merge
3. **Guard cascade** ordering

### Implementation

```typescript
// New function: extract specific fields from policy
function extractPolicyFields(
  policy: PolicyConfig,
  fields: string[]
): Partial<PolicyConfig> {
  const result: Partial<PolicyConfig> = {};
  for (const field of fields) {
    const value = getNestedField(policy, field);
    if (value \!== undefined) {
      setNestedField(result, field, value);
    }
  }
  return result;
}

// New function: remove fields from policy
function excludePolicyFields(
  policy: PolicyConfig,
  fields: string[]
): PolicyConfig {
  const result = { ...policy };
  for (const field of fields) {
    deleteNestedField(result, field);
  }
  return result;
}
```

## Guard Cascade (Section 8.2)

Order of guard execution:
1. Policy-generated guards (privileged)
2. Imported privileged guards (in import order)
3. Imported non-privileged guards (in import order)
4. Local guards (in declaration order)

Later guards override earlier on conflict (CSS-like).

### Implementation

```typescript
// In GuardRegistry
interface RegisteredGuard {
  guard: Guard;
  source: "policy" | "import" | "local";
  importOrder: number;
  privileged: boolean;
}

// Sort guards by cascade order
function sortGuardsByCascade(guards: RegisteredGuard[]): RegisteredGuard[] {
  return guards.sort((a, b) => {
    // Policy guards first
    if (a.source === "policy" && b.source \!== "policy") return -1;
    if (b.source === "policy" && a.source \!== "policy") return 1;
    
    // Then by privilege
    if (a.privileged && \!b.privileged) return -1;
    if (b.privileged && \!a.privileged) return 1;
    
    // Then by import order
    return a.importOrder - b.importOrder;
  });
}
```

## Files to Modify

1. `grammar/mlld.pegjs` - Import syntax
2. `core/policy/union.ts` - Field extraction/exclusion
3. `interpreter/eval/import/` - Handle selective imports
4. `interpreter/hooks/GuardRegistry.ts` - Cascade ordering

## Exit Criteria

- [ ] Selective import syntax parses: `import { allow.cmd } from ...`
- [ ] Except clause works: `import policy from ... except { deny }`
- [ ] Field extraction preserves nested structure
- [ ] Merge rules apply to extracted fields
- [ ] Guard cascade orders correctly
- [ ] Tests verify merge semantics

## References

- spec-security-2026-v4.md Section 8.1-8.2

## Estimated Effort

~6 hours


