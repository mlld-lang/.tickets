---
id: mlld-vgx2
status: open
deps: []
links: []
created: 2026-01-20T12:21:47.793601-08:00
type: task
priority: 1
tags: [size-l, complexity-l, risk-m, impl-partial, needs-human-design]
parent: mlld-si08
updated: 2026-01-31T00:35:23Z
---
# Security documentation overhaul for v4 spec

## Overview

~~The security documentation needs comprehensive updates to align with spec-security-2026-v4.md. Current documentation is approximately **40% complete** against the v4 spec.~~

**UPDATE 2026-01-29:** Now ~70% complete. docs/user/security.md (805 lines) covers:
- ✓ Policy structure and named policies
- ✓ Signing and verification (sign/verify)
- ✓ Autosign and autoverify
- ✓ Guards (before/after, composition, transforms)
- ✓ Labels and taint tracking
- ✓ Common patterns

**Still missing:**
- `influenced` label documentation
- `privileged: true` guard syntax
- Environments and env providers
- Audit ledger
- Some atomic files listed below

## Scope Expansion

Originally: Sync llms-security.txt direct edits to atoms
Now: Full security documentation overhaul across all doc types

---

## CRITICAL PRIORITY (Blocking v4 release)

### 1. Policy Documentation (Part 3) - **Missing Entirely**

Add to all docs:
- Complete `policy @name = { ... }` structure
- `defaults` object: `unlabeled`, `rules`, `autosign`, `autoverify`, `trustconflict`
- `sources` classification (`"src:mcp": untrusted`)
- `operations` classification
- `capabilities` syntax with patterns (`cmd:git:*`, `fs:r:**`)
- `danger` list concept
- Label flow rules with deny/allow, most-specific-wins, prefix matching
- Built-in rules: `no-secret-exfil`, `no-sensitive-exfil`, `no-untrusted-destructive`, `no-untrusted-privileged`, `untrusted-llms-get-influenced`
- Standard policies: `@mlld/production`, `@mlld/development`, `@mlld/sandbox`
- Policy composition rules (allow=intersection, deny=union, limits=minimum)
- **Policy is non-bypassable** (guards can be bypassed, policy cannot)

### 2. Credential Flow (Part 3.4) - **Incomplete**

Add:
- `policy.auth` configuration
- `using auth:name` sealed path syntax
- `using @var as ENV` explicit variable syntax
- Why credentials bypass deny rules (sealed path concept)
- Keychain policy: provider, allow/deny patterns

### 3. Environments (Part 5) - **Major Gap**

Add:
- Environments as THE primitive (credentials, isolation, capabilities, state)
- Environment configuration as values
- `env @config [ ... ]` block syntax
- Child derivation: `new @parent with { ... }`
- Attenuation invariant (children can only restrict)
- Hierarchical taint labels (`src:env:docker:sandbox:readonly`)
- Guards triggering environments: `=> env @config`
- Provider-specific config (fs, net, limits)
- Snapshots and session resume
- `@mcpConfig()` function pattern

### 4. MCP Integration (Part 6) - **Missing**

Add:
- MCP security tracking (`src:mcp` auto-applied)
- MCP policy integration via `policy.labels."src:mcp"`
- MCP configuration via `@mcpConfig()` function
- MCP server lifecycle

### 5. Environment Providers (Part 7) - **Missing**

Add:
- Provider interface: `@create`, `@execute`, `@release`, `@snapshot`
- Provider trust model (explicit trust grant via `provider:` field)
- Source labels from environments (`src:env:docker`, `src:env:sprites`)
- Defense in depth (OS-level + semantic guards)

---

## HIGH PRIORITY

### 6. Labels - Trust & Influence (Parts 1.1, 1.8)

Add:
- Label categories table (Trust, Sensitivity, Risk, Influence, Provenance, Operation, Custom)
- Trust labels (`trusted`/`untrusted`) - mutually exclusive
- Trust label asymmetry (untrusted wins unless privileged blessing)
- `influenced` label (auto-applied to LLM outputs when untrusted in context)
- `llm` label is user-declared on exe functions, not auto-applied

### 7. Operation Labels (Part 1.5)

Add/Update:
- Hierarchical pattern matching (`op:cmd:git` matches `op:cmd:git:*`)
- Auto-applied on directives vs user-declared on `exe` functions
- **Critical:** Operation labels do NOT propagate to outputs (go to `sources` field)
- User-declared semantic labels: `net:w`, `net:r`, `fs:r`, `fs:w`, `destructive`, `privileged`, `safe`

### 8. Privileged Guards (Parts 4.3, 4.6)

Add:
- `guard ... with { privileged: true }` syntax
- Privileged status preserved on import
- Privileged guards cannot be bypassed
- Label modification syntax: `=> label @var`, `=> trusted @var`, `=> trusted! @var` (blessing), `=> !label @var`, `=> clear! @var`
- Protected labels requiring privilege: `secret`, `untrusted`, all `src:*` labels
- `PROTECTED_LABEL_REMOVAL` error

### 9. Guard Bypass (Part 4.7)

Add:
- `with { guards: false }` syntax
- `with { guards: { only: [...] } }` and `with { guards: { except: [...] } }`
- `security.allowGuardBypass` config option

### 10. Signing & Verification (Part 14) - **Missing**

Add:
- Problem statement (auditor LLMs can be manipulated)
- Core insight: Sign templates, not interpolated results
- `sign @var with sha256`, `sign @var by "alice"`
- Storage in `.mlld/sec/sigs/`
- `verify @var` returns `{ verified, template, hash, signedby, signedat }`
- Auto-signing via `policy.defaults.autosign`
- Auto-verify for `llm`-labeled exes
- `MLLD_VERIFY_VARS` environment variable
- Guard enforcement: `@mx.tools.calls.includes("verify")`

### 11. Audit Ledger (Part 15) - **Missing**

Add:
- Location: `.mlld/sec/audit.jsonl`
- Events: sign, verify, label, bless, conflict, write
- File taint tracking
- Query API: `mlld audit why/history/signed/blessed`

---

## NEW ATOMIC FILES NEEDED (docs/src/atoms/security/)

1. policy-structure.md
2. policy-defaults.md
3. policy-capabilities.md
4. policy-sources.md
5. label-flow-rules.md
6. standard-policies.md
7. trust-labels.md
8. influence-label.md
9. operation-labels.md
10. credential-flow.md
11. privileged-guards.md
12. guard-bypass.md
13. environments.md
14. env-blocks.md
15. env-providers.md
16. mcp-integration.md
17. signing-basics.md
18. sign-directive.md
19. verify-directive.md
20. autosign-policy.md
21. autoverify-policy.md
22. audit-ledger.md
23. file-taint-tracking.md

---

## FILES TO UPDATE

### docs/user/security.md
- Restructure with policy as PRIMARY, guards as SECONDARY
- Add all new sections listed above
- Remove outdated `dir:/path` pattern references

### docs/dev/SECURITY.md
- Update spec reference to v4
- Add policy enforcement details
- Add privileged guards implementation
- Add credential injection flow
- Add environment provider interface

### docs/src/atoms/security/_index.md
- Expand to cover all security topics
- Add navigation to new atoms

### docs/llm/llms-security.txt
- Rebuild with updated atoms once atoms are complete
- Sync existing direct edits (SIGN_VERIFY, AUTOVERIFY, ENV_GUARDS) to atoms first

---

## ITEMS TO REMOVE/DEPRECATE

1. `dir:/path` pattern (replaced by hierarchical env labels)
2. Guard helpers (`@prefixWith`, `@tagValue`, `@input.any/all/none`) - verify existence
3. Load-time capability checking mentions (spec says "future consideration")
4. Old `jail` guard action references (replaced by `env` action)

---

## Original Task (Still Valid)

New sections added directly to llms-security.txt that need atom files:
- <SIGN_VERIFY> - sign and verify prompt content
- <AUTOVERIFY> - policy defaults for autosign/autoverify
- <ENV_GUARDS> - guards selecting execution environments


