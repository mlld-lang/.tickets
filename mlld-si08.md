---
id: mlld-si08
status: open
deps: [mlld-xbn2, mlld-yddg, mlld-8c8q, mlld-qptr]
links: []
created: 2026-01-18T11:08:54.861093-08:00
type: epic
priority: 1
parent: mlld-wmzl
---
# Security Model v4: Implementation

## Overview

This epic consolidates all security implementation work based on the v4 spec (`todo/spec-security-2026-v4.md`).

**Parent Epic:** mlld-wmzl (Security Model v3: Label Flow Implementation)

## v4 Spec Status

The v4 spec is **DESIGN COMPLETE** (2026-01-19). It supersedes v3 and includes:

- ✓ `defaults` object (unlabeled, rules, autosign, autoverify, trustconflict)
- ✓ Capability syntax (patterns, allow/deny, filesystem, network)
- ✓ Label modification syntax (=>, trusted\!, \!label, clear\!)
- ✓ Signing & verification primitives
- ✓ Audit ledger design
- ✓ `.mx.tools` namespace
- ✓ Policy merge semantics (selective imports, cascade)
- ✓ Keychain providers
- ✓ Path patterns (glob syntax)

## Current State (Already Implemented)

The **core prompt injection defense is COMPLETE** (P0 phases 1-4):
- ✓ Label flow enforcement working
- ✓ PolicyEnforcer runs before guards
- ✓ Credential injection via `using auth:*`
- ✓ Source labels on entry points
- ✓ Guard modification (blessing/tainting)
- ✓ Profiles for graceful degradation

## Dependency Graph (Parallel Tracks)

```
TRACK A: Policy DSL        TRACK B: Signing         TRACK C: Labels
────────────────────       ─────────────────        ───────────────
wmzl.15 (defaults)         si08.1 (primitives)      si08.4 (syntax)
    │                          │                        │
    ▼                          ▼                        ▼
dpgy (capabilities)        si08.2 (auto-sign)       si08.5 (influenced)
    │                          │                        │
    ▼                          ▼                        ▼
xbn2 (merge)               si08.3 (auto-verify)     qptr (trustconflict)
    │                          │
    ▼                          │
hncp (import guards)           │
    │                          │
    └──────────────────────────┴──────────────────────┐
                                                      ▼
                                            ┌─────────────────────┐
                                            │  AFTER P1 COMPLETE  │
                                            └─────────────────────┘
                                                      │
                        ┌─────────────────────────────┼─────────────────┐
                        ▼                             ▼                 ▼
                  wmzl.12                       si08.6            Infrastructure
               (std policies)               (audit ledger)      (8c8q, yddg, si08.7)
```

## Agent Assignment

| Agent | Track | Beads | Notes |
|-------|-------|-------|-------|
| **Agent A** | Policy DSL | wmzl.15 → dpgy → xbn2 → hncp | Grammar + PolicyConfig. Start first (longest chain) |
| **Agent B** | Signing | si08.1 → si08.2 → si08.3 | New primitives + storage. Self-contained |
| **Agent C** | Labels | si08.4, si08.5, qptr | Interpreter changes. Parallel to A & B |
| **Agent D** | Infrastructure | 8c8q, yddg, si08.6, si08.7 | P2, after core complete |

## Remaining Work

### Track A: Policy DSL (P1)

| Bead | Task | Status |
|------|------|--------|
| mlld-wmzl.15 | Implement `defaults` object | Ready |
| mlld-dpgy | Capability enforcement (allow-list, patterns) | Ready |
| mlld-xbn2 | Policy merge (selective imports, cascade) | Needs planning |
| mlld-hncp | Import policy guard registration | Ready |

### Track B: Signing & Verification (P1)

| Bead | Task | Status |
|------|------|--------|
| mlld-si08.1 | Signing primitives (`sign`, `verify`) | Ready |
| mlld-si08.2 | Auto-sign implementation | Ready |
| mlld-si08.3 | Auto-verify for LLM exes | Ready |

### Track C: Label System (P1)

| Bead | Task | Status |
|------|------|--------|
| mlld-si08.4 | Label modification syntax | Ready |
| mlld-si08.5 | `influenced` propagation | Ready |
| mlld-qptr | trustconflict behavior | Ready |

### Track D: Infrastructure (P2)

| Bead | Task | Status |
|------|------|--------|
| mlld-8c8q | Path patterns (glob syntax) | Needs planning |
| mlld-yddg | Keychain providers | Needs planning |
| mlld-si08.6 | Audit ledger | Ready |
| mlld-si08.7 | `.mx.tools` namespace | Ready |

### Cleanup & Finalization (P2)

| Bead | Task | Status |
|------|------|--------|
| mlld-8r2w | Remove /needs keychain gating | Ready |
| mlld-tzwq | Review v4 for implementation gaps | After impl |
| mlld-wmzl.12 | Standard policy modules | Blocked on P1 |

### Environments (P2, separate track)

| Bead | Task |
|------|------|
| mlld-h47o | Env block syntax |
| mlld-9oyx | Capture skills |

## Priority Order

**P1 (Core Security) - Can parallelize A/B/C:**
1. wmzl.15, si08.1, si08.4 (start together)
2. dpgy, si08.2, si08.5 (after first wave)
3. xbn2, si08.3, qptr (after second wave)
4. hncp (after xbn2)

**P2 (Polish) - After P1:**
5. si08.6, si08.7, 8c8q, yddg
6. wmzl.12 (standard policies)
7. tzwq (final gap review)

## References

- `todo/spec-security-2026-v4.md` - Authoritative spec
- `todo/spec-security-2026-v3.md` - Superseded


