---
id: mlld-8ka9
status: closed
deps: []
links: []
created: 2026-01-17T15:56:07.828628-08:00
type: epic
priority: 1
---
# Workstream A: Credential Flow & Polish

## Overview

Complete the credential flow system and polish the core security implementation. This workstream covers the remaining polish on top of P0.

**Parent Epic:** mlld-wmzl (Security Model v3)

## Phases Included

| Phase | Bead | Effort | Status |
|-------|------|--------|--------|
| 2.5 | mlld-wmzl.13 | 2h | /needs verification |
| 4.6 | mlld-wmzl.11 | 2h | Structured denial messages |

**Total:** ~4 hours

## What This Achieves

After this workstream:
- ✓ `/needs` works correctly for dependencies (not security)
- ✓ Policy denials have helpful error messages with suggestions

## Dependencies

**Requires:** P0 complete (Phases 1-4) ✓
**Blocks:** None

## Complexity

**Low** - All straightforward work:
- Phase 2.5: Verify existing /needs behavior, maybe add validation
- Phase 4.6: Format error messages nicely

## Starting Point

**Start with Phase 2.5** (mlld-wmzl.13):
```
bd show mlld-wmzl.13
```

/needs investigation is done (see comments). Likely just needs verification that it works for package deps + command availability.

## References

- Main epic: `bd show mlld-wmzl`
- Spec: `todo/spec-security-2026-v3.md`
- Plan: `todo/plan-security-2026-v3.md`


