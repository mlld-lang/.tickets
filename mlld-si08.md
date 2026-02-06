---
id: mlld-si08
status: closed
deps: []
links: []
created: 2026-01-18T11:08:54.861093-08:00
type: epic
priority: 2
tags: [size-m, complexity-m, risk-m, impl-done]
parent: mlld-wmzl
updated: 2026-01-30T06:50:45Z
---
# Security Model v4: Implementation

## Status: CORE COMPLETE

The **core security implementation is DONE**. All P0/P1 work has been completed. Only P2 polish items remain as separate tickets.

## What's Implemented

### Core Defense (P0) - COMPLETE
- ✓ Label flow enforcement
- ✓ PolicyEnforcer runs before guards
- ✓ Credential injection via `using auth:*`
- ✓ Source labels on entry points
- ✓ Guard modification (blessing/tainting)
- ✓ Profiles for graceful degradation

### Track A: Policy DSL - COMPLETE
- ✓ wmzl.15 - `defaults` object
- ✓ dpgy - Capability enforcement
- ✓ hncp - Import policy guard registration
- (deferred) xbn2 - Policy merge/composition

### Track B: Signing & Verification - COMPLETE
- ✓ si08.1 - Signing primitives
- ✓ si08.2 - Auto-sign
- ✓ si08.3 - Auto-verify for LLM exes

### Track C: Label System - COMPLETE
- ✓ si08.4 - Label modification syntax
- ✓ si08.5 - `influenced` propagation
- ✓ qptr - trustconflict behavior

### Track D: Infrastructure - MOSTLY COMPLETE
- ✓ 8c8q - Path patterns (glob syntax)
- ✓ si08.7 - `.mx.tools` namespace
- ✓ 8r2w - Remove /needs keychain gating
- ✓ tzwq - v4 spec review
- ✓ h47o - Env block syntax

## Remaining P2 Polish (separate tickets)

| Ticket | Task | Priority |
|--------|------|----------|
| mlld-yddg | Keychain providers (system, 1password) | P2 |
| mlld-si08.6 | Audit ledger | P2 |
| mlld-wmzl.12 | Standard policy modules (@mlld/*) | P2 |
| mlld-xbn2 | Policy merge/composition | Deferred |

These are tracked as independent tickets, not blocking this epic.

## References

- `todo/spec-security-2026-v4.md` - Authoritative spec
