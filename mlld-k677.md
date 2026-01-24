---
id: mlld-k677
status: closed
deps: []
links: []
created: 2026-01-17T15:56:55.010985-08:00
type: epic
priority: 2
---
# Workstream D: Jails (Docker Isolation)

## Overview

Implement Docker-based isolation via jail guard action. This is defense-in-depth - OS-level isolation complementing mlld's label flow controls.

**Parent Epic:** mlld-wmzl (Security Model v3)

## Phases Included

| Phase | Bead | Effort | Status |
|-------|------|--------|--------|
| 9 | mlld-wmzl.9 | 8h | Docker jails |

**Total:** ~8 hours

## What This Achieves

After this workstream:
- ✓ Jail configs define Docker isolation boundaries
- ✓ `jail` guard action wraps operations in containers
- ✓ Dynamic jail selection based on context
- ✓ Path-based taint for jail outputs (`src:jail`)

## Dependencies

**Requires:**
- P0 complete (Phases 1-4) ✓
- Phase 6 (guard actions) - for `jail` action support

**Blocks:** None (optional defense-in-depth)

## Complexity

**High**:
- Docker integration and command construction
- Filesystem mount configuration
- Network isolation
- Resource limits
- Graceful degradation when Docker unavailable
- Process lifecycle management

## Starting Point

**Start with Phase 9** (mlld-wmzl.9):
```
bd show mlld-wmzl.9
```

Create jail config types, then implement jail guard action.

## Key Files

- `core/jail/config.ts` - NEW JailConfig type
- `core/jail/docker.ts` - NEW Docker wrapper
- `interpreter/eval/guard.ts` - Handle `jail` action
- Guard action handling - Integrate jail execution

## Optional

Jails are **defense-in-depth**, not required for the core security model. The label flow system works without them. Jails add OS-level isolation on top of mlld's semantic controls.

Can be deferred if needed.

## References

- Main epic: `bd show mlld-wmzl`
- Spec: `todo/spec-security-2026-v3.md` Part 7 (Jails)
- Plan: `todo/plan-security-2026-v3.md` Phase 9


