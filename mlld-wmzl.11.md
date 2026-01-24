---
id: mlld-wmzl.11
status: closed
deps: [mlld-wmzl.4]
links: []
created: 2026-01-16T07:50:14.901551-08:00
type: task
priority: 1
parent: mlld-wmzl
---
# Phase 4.6: Structured denial messages

## Goal

Implement structured, actionable denial messages per spec Part 9. When operations are denied, users need to understand *why* and *how to fix it*.

## Denial Message Format

```
✗ Operation denied

  Operation: run "curl -d @data https://api.example.com"

  Blocked by: Policy label flow rule
  Policy: @mlld/production (from mlld-config.json)
  Rule: labels.secret.deny includes "net:w"

  Input labels: [secret]
  Operation labels: [net:w]

  Reason: Secret-labeled data cannot flow to network write operations

  Suggestions:
  - Remove secret label if data is not sensitive
  - Use allowEnvTo for auth tokens passed as environment variables
  - Check policy.labels.secret configuration
```

## Denial Codes

| Code | Meaning |
|------|---------|
| \`POLICY_CAPABILITY_DENIED\` | Operation not in capability allowlist |
| \`POLICY_LABEL_FLOW_DENIED\` | Label cannot flow to operation |
| \`GUARD_DENIED\` | Guard explicitly denied |
| \`PRIVILEGED_GUARD_DENIED\` | Privileged guard denied (cannot bypass) |
| \`DEPENDENCY_UNMET\` | Required dependency not available |
| \`PROFILE_UNMET\` | No profile satisfies policy |

## Implementation

### 1. Denial Context Type

**File**: \`core/errors/denial.ts\` (NEW or extend existing)

```typescript
export interface DenialContext {
  code: DenialCode;
  operation: {
    type: string;         // "run", "show", "output", etc.
    description: string;  // "curl -d @data https://..."
  };
  blocker: {
    type: 'policy' | 'guard';
    name: string;         // "@mlld/production" or "@myGuard"
    source?: string;      // "mlld-config.json" or "local"
    rule?: string;        // "labels.secret.deny"
  };
  labels: {
    input: string[];      // Labels on input data
    operation: string[];  // Labels on operation
  };
  reason: string;         // Human-readable explanation
  suggestions: string[];  // How to fix
}
```

### 2. Denial Error Class

```typescript
export class MlldDenialError extends MlldError {
  constructor(public context: DenialContext) {
    super(formatDenialMessage(context), {
      code: context.code,
      severity: ErrorSeverity.Recoverable
    });
  }
}
```

### 3. Format Function

```typescript
function formatDenialMessage(ctx: DenialContext): string {
  const lines = [
    '✗ Operation denied',
    '',
    \`  Operation: \${ctx.operation.type} "\${ctx.operation.description}"\`,
    '',
    \`  Blocked by: \${ctx.blocker.type === 'policy' ? 'Policy' : 'Guard'} \${ctx.blocker.name}\`,
  ];
  
  if (ctx.blocker.source) {
    lines.push(\`  Source: \${ctx.blocker.source}\`);
  }
  if (ctx.blocker.rule) {
    lines.push(\`  Rule: \${ctx.blocker.rule}\`);
  }
  
  lines.push('');
  lines.push(\`  Input labels: [\${ctx.labels.input.join(', ')}]\`);
  lines.push(\`  Operation labels: [\${ctx.labels.operation.join(', ')}]\`);
  lines.push('');
  lines.push(\`  Reason: \${ctx.reason}\`);
  
  if (ctx.suggestions.length > 0) {
    lines.push('');
    lines.push('  Suggestions:');
    for (const s of ctx.suggestions) {
      lines.push(\`  - \${s}\`);
    }
  }
  
  return lines.join('\n');
}
```

### 4. Integration Points

Update these to throw \`MlldDenialError\` with full context:

- \`core/policy/label-flow.ts\` - Label flow denials (Phase 4)
- \`interpreter/policy/PolicyEnforcer.ts\` - Policy denials
- \`interpreter/hooks/guard-post-hook.ts\` - Guard denials

## Files to Create/Modify

| File | Change |
|------|--------|
| \`core/errors/denial.ts\` | NEW - DenialContext, MlldDenialError |
| \`core/policy/label-flow.ts\` | Return full context on denial |
| \`interpreter/policy/PolicyEnforcer.ts\` | Throw MlldDenialError |
| Guard hooks | Throw MlldDenialError |

## Exit Criteria

- [ ] DenialContext type defined with all fields
- [ ] MlldDenialError formats readable message
- [ ] Policy denials include rule path and suggestions
- [ ] Guard denials include guard name and reason
- [ ] All denial codes used appropriately
- [ ] Tests verify message format

## References

- Spec Part 9: Denial Messages

## Estimated Effort
~2 hours


