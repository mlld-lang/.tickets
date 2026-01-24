---
id: mlld-0qat
status: closed
deps: []
links: []
created: 2026-01-17T15:56:23.694131-08:00
type: epic
priority: 1
---
# Workstream B: Source Labels & Guard Modification

## Overview

Complete the source label system and guard modification capabilities. This enables full data provenance tracking and guard-based blessing.

**Parent Epic:** mlld-wmzl (Security Model v3)

## Phases Included

| Phase | Bead | Effort | Status |
|-------|------|--------|--------|
| 5 | mlld-wmzl.5 | 3h | src:* source labels |
| 6 | mlld-wmzl.6 | 3h | Guard label modification |
| 4.5 | mlld-wmzl.10 | 2h | Guard bypass syntax |

**Total:** ~8 hours

## What This Achieves

After this workstream:
- ✓ All data entry points labeled (`src:exec`, `src:file`, `src:network`)
- ✓ Guards can add/remove labels for blessing workflows
- ✓ Protected labels enforced (only privileged guards can remove `src:*`)
- ✓ `with { guards: false/only/except }` syntax works

## Dependencies

**Requires:** P0 complete (Phases 1-4) ✓
**Blocks:** Workstream D (jails use guard actions)

## Complexity

**Medium** - Moderate complexity:
- Phase 5: Repetitive (apply labels at entry points) - LOW
- Phase 6: Guard system extension, privilege checking - MEDIUM
- Phase 4.5: Grammar + evaluator for bypass - LOW

## Starting Point

**Start with Phase 5** (mlld-wmzl.5):
```
bd show mlld-wmzl.5
```

Apply `src:exec`, `src:file`, `src:network` at data entry points. `src:mcp` and `src:dynamic` already exist as patterns.

## Key Files

- `interpreter/eval/run.ts` - src:exec on command output
- `interpreter/eval/content-loader.ts` - src:file on file reads
- `core/resolvers/HTTPResolver.ts` - src:network on fetches
- `interpreter/hooks/guard-post-hook.ts` - Label modification
- `grammar/patterns/with-clause.peggy` - Guard bypass syntax

## References

- Main epic: `bd show mlld-wmzl`
- Spec: `todo/spec-security-2026-v3.md` Part 1 (Source Labels), Part 4 (Guards)
- Plan: `todo/plan-security-2026-v3.md` Phases 5, 6, 4.5


