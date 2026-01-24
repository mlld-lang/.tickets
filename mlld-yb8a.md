---
id: mlld-yb8a
status: closed
deps: []
links: []
created: 2026-01-14T15:16:10.541973-08:00
type: task
priority: 0
parent: mlld-7suk
---
# Phase 1.2: Expose @mx.taint to guards [SECURITY-CRITICAL]

## Summary
Expose `@mx.taint` to guard expressions so guards can check data provenance (where data came from).

## Security Impact
**CRITICAL**: Without this, guards cannot detect LLM-influenced data. The security model depends on tracking provenance through `src:mcp`, `src:exec`, `src:file` markers.

## Context
Currently, guards can access `@mx.labels` and `@mx.sources` but NOT `@mx.taint`. The taint includes labels PLUS source markers (`src:*`, `dir:*`). This is required for guards like:
```mlld
/guard before op:run = when [
  @mx.taint.includes("src:mcp") => deny "Cannot execute MCP-originated operations"
  * => allow
]
```

## Implementation

### Current State (from audit-guards.md)
Location: `interpreter/hooks/guard-pre-hook.ts:866-880`

```typescript
const guardContext: GuardContextSnapshot = {
  labels: contextLabels,
  sources: contextSources,
  // taint: NOT INCLUDED - ADD THIS
}
```

### Required Changes

1. **Add taint to GuardContextSnapshot interface**
   File: `core/types/guard.ts`
   ```typescript
   interface GuardContextSnapshot {
     labels: string[];
     sources: string[];
     taint: string[];  // ADD THIS
   }
   ```

2. **Populate taint in guard context building**
   File: `interpreter/hooks/guard-pre-hook.ts:866-880`
   - Extract taint from `security?.taint` 
   - Add to GuardContextSnapshot

3. **Expose in ContextManager**
   File: `interpreter/env/ContextManager.ts`
   - Prefer guard-context taint in `buildAmbientContext` when present
   - Add `taint` field to ambient context shape

## Exit Criteria
- [ ] Guard expressions can access `@mx.taint`
- [ ] `@mx.taint.includes("src:mcp")` works in guard conditions
- [ ] `@mx.taint.includes("src:exec")` works in guard conditions
- [ ] `@mx.taint` includes both labels AND source markers
- [ ] All existing guard tests pass
- [ ] Build succeeds with no type errors

## Tests to Add
Create `tests/cases/security/guard-taint-access/`
```
example.md:
  /var @data = "test"
  /guard @checkTaint before show = when [
    @mx.taint.includes("src:exec") => deny "Blocked"
    * => allow
  ]
  /show @data
expected.md:
  test
```

Create `tests/cases/security/guard-taint-blocks-mcp/`
(Integration test with MCP - may need mocking)

## Dependencies
None - can start immediately

## Estimated Effort
2 hours


