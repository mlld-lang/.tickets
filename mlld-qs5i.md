---
id: mlld-qs5i
status: closed
deps: []
links: []
created: 2026-01-17T15:56:40.965982-08:00
type: epic
priority: 1
---
# Workstream C: Profiles & MCP Lifecycle

## Overview

Complete the environment module system: profiles for graceful degradation and MCP server lifecycle orchestration.

**Parent Epic:** mlld-wmzl (Security Model v3)

## Phases Included

| Phase | Bead | Effort | Status |
|-------|------|--------|--------|
| 7 | mlld-wmzl.7 | 4h | /profiles directive |
| 8 | mlld-wmzl.8 | 6h | MCP server lifecycle |

**Total:** ~10 hours

## What This Achieves

After this workstream:
- ✓ Modules declare profiles for graceful degradation
- ✓ `@mx.profile` available in ContextManager
- ✓ Environment modules work: @spawn, @shell, @mcpConfig
- ✓ MCP servers spawn based on @mcpConfig() output
- ✓ Full environment functionality operational

## Dependencies

**Requires:** 
- P0 complete (Phases 1-4) ✓
- Phase 3.5 (`using` keyword) ✓ - environments use `using auth:name`

**Blocks:** None (can ship environments without jails)

## Complexity

**Medium-High**:
- Phase 7: Grammar + evaluator, ContextManager integration - MEDIUM
- Phase 8: Process lifecycle, aggregation, cleanup - HIGH

Phase 8 is the most complex remaining P1 work (MCP orchestration, multi-server aggregation).

## Starting Point

**Start with Phase 7** (mlld-wmzl.7):
```
bd show mlld-wmzl.7
```

Implement `/profiles` directive to replace `/wants`. This is cleaner than Phase 8 and provides foundation for environment testing.

## Key Files

**Phase 7:**
- `grammar/directives/needs-wants.peggy` - Add /profiles grammar
- `interpreter/eval/profiles.ts` - NEW evaluator
- `interpreter/env/ContextManager.ts` - Store @mx.profile
- `core/types/primitives.ts` - Add DirectiveKind.profiles

**Phase 8:**
- `cli/commands/env.ts` - Call @mcpConfig(), spawn servers
- `cli/mcp/MCPOrchestrator.ts` - NEW server lifecycle manager
- Process management, cleanup, aggregation

## References

- Main epic: `bd show mlld-wmzl`
- Spec: `todo/spec-security-2026-v3.md` Part 2.5 (Profiles), Part 5-6 (Environments, MCP)
- Plan: `todo/plan-security-2026-v3.md` Phases 7-8
- Related: mlld-ohya (MCP lifecycle bug - overlaps with Phase 8)


