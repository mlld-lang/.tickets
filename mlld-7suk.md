---
id: mlld-7suk
status: closed
deps: []
links: []
created: 2026-01-14T16:01:26.63721-08:00
type: task
priority: 0
parent: mlld-qs0y
---
# Workstream B: Taint & MCP Security [SECURITY-CRITICAL]

## Workstream Overview

This workstream implements taint tracking exposure and complete MCP security tracking - core to the security model.

**Parent Epic**: mlld-qs0y (MCP, Environment Modules, and Policy Integration)

## Why This Matters

Without this workstream, guards cannot:
- Detect data that originated from LLM/MCP calls
- Block operations influenced by untrusted sources
- Track provenance through transformations

The entire security model depends on `@mx.taint` and `src:mcp` tracking.

## Sequence

```
mlld-yb8a (1.2 Expose @mx.taint) [2h] 🔒
    │
    ├──→ mlld-1od5 (1.3 Document security model) [4h]
    │
    └──→ mlld-atyp (3.1 MCP security tracking) [6h] 🔒
```

## Beads in This Workstream

1. **mlld-yb8a** - Phase 1.2: Expose @mx.taint to guards [SECURITY-CRITICAL]
   - Add `taint` to GuardContextSnapshot interface
   - Populate from security descriptor in guard context
   - Enable `@mx.taint.includes("src:mcp")` checks

2. **mlld-1od5** - Phase 1.3: Document security tracking model
   - Document three dimensions (labels, taint, sources)
   - Add Part 1.5 to spec
   - Update guard and MCP documentation

3. **mlld-atyp** - Phase 3.1: Apply complete MCP security tracking [SECURITY-CRITICAL]
   - Apply security descriptors to MCP arguments
   - Create invocation-level descriptors (fixes zero-arg bypass)
   - Merge into guard context and result descriptors

## Key Files

```
interpreter/hooks/guard-pre-hook.ts:866-880 (taint exposure)
core/types/guard.ts (GuardContextSnapshot)
cli/mcp/FunctionRouter.ts:71-102 (MCP tracking)
interpreter/eval/exec-invocation.ts (result descriptors)
```

## Security Model Reference

```
@mx.labels  - Semantic: secret, pii, untrusted
@mx.taint   - Provenance: src:mcp, src:exec, src:file, dir:/path
@mx.sources - Trail: mcp:createIssue, command:curl, guard:@sanitize
```

MCP calls must populate all three:
```
labels: ["untrusted"]
taint: ["src:mcp"]  
sources: ["mcp:toolName"]
```

## Context Documents

- `todo/audits/audit-guards.md` - Guard system gaps (taint not exposed)
- `todo/audits/audit-mcp.md` - MCP tracking gaps
- `how-mlld-prevents-consequences-of-injection.md` - Security philosophy

## Exit Criteria

- [ ] Guards can access `@mx.taint`
- [ ] `@mx.taint.includes("src:mcp")` works in guard conditions
- [ ] Zero-arg MCP tools still carry `src:mcp` taint
- [ ] MCP tool outputs have both `src:exec` and `src:mcp`
- [ ] Documentation complete for three-dimensional model
- [ ] Tests in `tests/cases/security/` verify tracking

## Estimated Effort
12 hours total (2h + 4h + 6h)

## Tests to Add

```
tests/cases/security/guard-taint-access/
tests/cases/security/mcp-taint/arg-tracking/
tests/cases/security/mcp-taint/zero-arg-tracking/
tests/cases/security/mcp-taint/output-tracking/
```


