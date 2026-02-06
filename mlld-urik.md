---
id: mlld-urik
status: closed
deps: []
links: []
created: 2026-01-15 10:54:31 -0800
type: task
priority: 1
tags: [size-xl, complexity-xl, risk-xl]
---
# EPIC: Remediation Completeness (Post-Audit Round 2)

## Overview

Post-audit completeness epic. Combines findings from:
1. My agent audits (Tracks A-D review)
2. gpt52's deeper analysis (critical policy issues)

## Status Summary

| Track | Status | Notes |
|-------|--------|-------|
| **A: Security Model** | ✅ COMPLETE | Verified by audit |
| **B: Contract** | ✅ COMPLETE | Verified by audit |
| **C: Capability** | ⚠️ GAPS | C1/C3 done, C2/C4 incomplete |
| **D: Tooling** | ⚠️ GAPS | D4 done, D1/D2/D3 incomplete |
| **Policy Model** | ❌ CRITICAL GAPS | gpt52 findings |

## gpt52 Critical Findings

1. **/wants ignores policy allow constraints** (mlld-905x)
   - `getPolicyCapabilities` defaults to ALLOW_ALL
   - `policyConfigPermitsTier` only checks deny, not allow
   - Tiers can grant capabilities beyond policy allows

2. **/import policy does not register guards** (mlld-hncp)
   - Only `/policy` directive registers guards
   - `/import policy` imports config but doesn't enforce

3. **Policy guard generation incomplete** (mlld-dpgy)
   - Only covers /run denies
   - Missing: /exe, filesystem, allow-based constraints

4. **MCP server lifecycle missing** (mlld-ohya)
   - @mcpConfig() called but result discarded
   - No server spawning or cleanup

5. **Environment module type not installable** (mlld-7prj)
   - ModuleInstaller excludes 'environment'
   - Can't scaffold or install env modules

6. **@spawn signature mismatch** (mlld-rkjr)
   - Template expects prompt string
   - CLI passes argv array

7. **/needs enforcement disabled in tests** (mlld-de5w)
   - Tests bypass enforcement
   - Can't verify capability gating works

## All Beads (12 total)

| Priority | ID | Title |
|----------|-----|-------|
| P0 | mlld-905x | /wants ignores policy allow |
| P0 | mlld-hncp | /import policy no guards |
| P1 | mlld-dpgy | Guard generation incomplete |
| P1 | mlld-ohya | MCP lifecycle missing |
| P1 | mlld-7prj | Env type not installable |
| P1 | mlld-rkjr | @spawn signature mismatch |
| P1 | mlld-h138 | C2-GAP: Capability gating |
| P1 | mlld-b2nu | C4-GAP: Analyze detection |
| P2 | mlld-de5w | /needs test enforcement |
| P2 | mlld-2kjj | D1-GAP: Helper modules |
| P2 | mlld-8f15 | D2-GAP: Publish verification |
| P2 | mlld-dgcu | D3-GAP: Documentation |

## Exit Criteria

- [ ] Policy allow constraints enforced in tier selection
- [ ] /import policy registers guards
- [ ] Guard generation covers all operations
- [ ] MCP servers spawn and cleanup properly
- [ ] Environment modules installable
- [ ] @spawn receives correct arguments
- [ ] /needs enforcement verified in tests
- [ ] All helper modules exist
- [ ] Publish verification bidirectional
- [ ] Documentation complete

## Estimated Effort
~40-55 hours total


