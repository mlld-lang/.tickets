---
id: mlld-wmzl.6
status: closed
deps: [mlld-wmzl.4]
links: []
created: 2026-01-16T07:19:12.655203-08:00
type: task
priority: 1
parent: mlld-wmzl
---
# Phase 6: Guard label modification (addLabels/removeLabels)

## Goal

Guards can add/remove labels via \`allow with { addLabels: [...], removeLabels: [...] }\`. This enables "blessing" data after validation.

**CRITICAL**: Removing security-critical labels requires **privileged guards**.

## Protected Labels

Labels that can only be removed by privileged guards:

```typescript
const PROTECTED_LABELS = [
  'secret',
  'src:mcp',
  'src:exec',
  'src:file',
  'src:network',
  'src:user',
  'src:jail',
  'src:dynamic'
];

function isProtectedLabel(label: string): boolean {
  return PROTECTED_LABELS.includes(label) || label.startsWith('src:');
}
```

## Guard Action Extension

**File**: `interpreter/eval/guard.ts` or `interpreter/hooks/`

```typescript
interface GuardAllowAction {
  type: 'allow';
  value?: any;           // Optional transformed value
  addLabels?: string[];
  removeLabels?: string[];
  warning?: string;
}
```

## Apply Label Modifications

```typescript
function applyLabelModifications(
  descriptor: SecurityDescriptor,
  action: GuardAllowAction,
  guardContext: { isPrivileged: boolean }
): SecurityDescriptor {
  let labels = [...descriptor.labels];
  let taint = [...descriptor.taint];

  if (action.removeLabels) {
    for (const label of action.removeLabels) {
      // Privileged guards required for protected labels
      if (isProtectedLabel(label) && !guardContext.isPrivileged) {
        throw new MlldGuardError(
          \`Cannot remove protected label '\${label}' from non-privileged guard\`,
          { code: 'PROTECTED_LABEL_REMOVAL' }
        );
      }
    }
    labels = labels.filter(l => !action.removeLabels!.includes(l));
    taint = taint.filter(t => !action.removeLabels!.includes(t));
  }

  if (action.addLabels) {
    labels = [...new Set([...labels, ...action.addLabels])];
    taint = [...new Set([...taint, ...action.addLabels])];
  }

  return makeSecurityDescriptor({
    ...descriptor,
    labels,
    taint
  });
}
```

## Use Case: Blessing MCP Data

```mlld
/guard @validateMcpOutput after "src:mcp" = when [
  @output.schema.valid => allow with { removeLabels: ["src:mcp"] }
  * => deny "Schema validation failed"
]
```

This guard (if privileged) can "bless" MCP data after validation, removing the `src:mcp` taint so subsequent operations aren't blocked.

## Privileged Guards

Guards are privileged when:
1. Generated from `/policy` directive
2. Imported with `privileged` keyword: \`/import guards privileged from "..."\`

**File**: Track privilege status in guard context during execution.

## Files to Modify

| File | Change |
|------|--------|
| `core/types/security.ts` | Add PROTECTED_LABELS constant |
| `interpreter/eval/guard.ts` | Handle addLabels/removeLabels |
| `interpreter/hooks/guard-post-hook.ts` | Apply label modifications |

## Exit Criteria

- [ ] Guards can specify `addLabels` in allow action
- [ ] Guards can specify `removeLabels` in allow action
- [ ] Protected labels require privileged guard to remove
- [ ] Non-privileged guard throws PROTECTED_LABEL_REMOVAL error
- [ ] Tests verify label addition works
- [ ] Tests verify privileged guard can remove src:mcp
- [ ] Tests verify non-privileged guard cannot remove src:mcp

## References

- Spec Section 4.3: Guard Actions (allow with label modification)
- Spec Section 4.6: Privileged Guards
- Plan Phase 6: `todo/plan-security-2026-v3.md` lines 533-615

## Estimated Effort
~3 hours


