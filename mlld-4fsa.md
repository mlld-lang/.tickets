---
id: mlld-4fsa
status: closed
deps: []
links: []
created: 2026-01-14T20:01:13.268761-08:00
type: task
priority: 0
parent: mlld-vr3w
---
# Track A: Security Model Fixes [CRITICAL]

## Track Overview

Fix the three critical security model gaps that break the core security guarantees.

**Parent Epic**: mlld-vr3w

## Why This Track is Critical

Without these fixes:
- Guards cannot see MCP-originated data (`@mx.taint` empty)
- Keychain values leak without `secret` label protection
- Any module can access keychain without declaring capability

The entire execution-layer security model is compromised.

## Beads in This Track

### A1: Fix MCP taint timing [CRITICAL]
- Guard pre-hooks run BEFORE mcpSecurity is merged into context
- Guards see empty `@mx.taint` and `@mx.sources` for MCP calls
- Need to inject mcpSecurity into guard context earlier

### A2: Label keychain values as secret [CRITICAL]
- `keychain.get()` returns raw strings
- No security descriptor on return values
- Guards can't detect secrets for exfiltration blocking

### A3: Gate keychain by /needs declaration [CRITICAL]
- Any script can `import { get } from @keychain`
- No check for `/needs { keychain }` declaration
- First step toward capability gating framework

## Sequence

```
A1 (MCP taint) ──┐
                 ├──→ Security model functional
A2 (secret label)┤
                 │
A3 (needs gate) ─┴──→ Feeds into Track C (capability framework)
```

All three can run in parallel - no dependencies between them.

## Key Files

```
interpreter/eval/exec-invocation.ts:1835,1878,1948
interpreter/hooks/guard-pre-hook.ts
cli/mcp/FunctionRouter.ts:64,107
core/resolvers/builtin/KeychainResolver.ts:55,106
```

## Exit Criteria

- [ ] `@mx.taint.includes("src:mcp")` returns true in guards for MCP calls
- [ ] `@mx.sources.includes("mcp:toolName")` returns true in guards
- [ ] Keychain values have `mx.labels: ["secret"]`
- [ ] `@keychain` import fails without `/needs { keychain }`
- [ ] Tests verify all three properties

## Estimated Effort
8-11 hours total


