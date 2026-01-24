---
id: mlld-wmzl.1
status: closed
deps: []
links: []
created: 2026-01-16T07:17:22.255389-08:00
type: task
priority: 0
parent: mlld-wmzl
---
# Phase 1: Remove untrusted auto-labeling

## Goal

Stop auto-applying `untrusted` label. Per spec v3, convention labels like `untrusted` are **NEVER automatically applied**. If you want `src:mcp` data treated specially, write policy rules for `src:mcp` directly.

## Current State (Spec Violations)

Two locations auto-apply `untrusted`:

### 1. FunctionRouter.ts (MCP tool results)

**File**: `cli/mcp/FunctionRouter.ts`
**Line**: ~80

```typescript
// CURRENT (WRONG):
return makeSecurityDescriptor({
  labels: ['untrusted'],  // ← REMOVE THIS
  taint: ['src:mcp'],
  sources: [\`mcp:\${toolName}\`]
});

// CORRECT:
return makeSecurityDescriptor({
  labels: [],
  taint: ['src:mcp'],
  sources: [\`mcp:\${toolName}\`]
});
```

### 2. ImportDirectiveEvaluator.ts (dynamic modules)

**File**: `interpreter/eval/import/ImportDirectiveEvaluator.ts`
**Line**: ~1059

```typescript
// CURRENT (WRONG):
return makeSecurityDescriptor({
  labels: ['untrusted' as DataLabel],  // ← REMOVE THIS
  taint: snapshot?.taint ?? ['src:dynamic'],
  ...
});

// CORRECT:
return makeSecurityDescriptor({
  labels: [],
  taint: snapshot?.taint ?? ['src:dynamic'],
  ...
});
```

## Test Updates

**File**: `cli/mcp/FunctionRouter.test.ts`

Update any tests that expect `untrusted` label:
- Keep expectations for `src:mcp` taint (correct)
- Remove expectations for `untrusted` label (spec violation)

## Verification

After changes:
```typescript
// MCP tool result should have:
{
  labels: [],                    // NO automatic labels
  taint: ['src:mcp'],            // Automatic source tracking
  sources: ['mcp:toolName']      // Transformation trail
}

// Dynamic module content should have:
{
  labels: [],                    // NO automatic labels
  taint: ['src:dynamic'],        // Automatic source tracking
  ...
}
```

## Exit Criteria

- [ ] `FunctionRouter.ts` returns `labels: []` for MCP results
- [ ] `ImportDirectiveEvaluator.ts` returns `labels: []` for dynamic modules
- [ ] Tests updated to not expect `untrusted` label
- [ ] Tests still verify `src:mcp` and `src:dynamic` taint works

## References

- Spec Section 1.1: "No magic: Convention labels like `untrusted` are NEVER automatically applied"
- Plan Phase 1: `todo/plan-security-2026-v3.md` lines 95-145

## Estimated Effort
~1 hour


