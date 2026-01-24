---
id: mlld-j5bw
status: closed
deps: []
links: []
created: 2026-01-14T20:04:02.331852-08:00
type: task
priority: 1
parent: mlld-vr3w
---
# Track C: Capability Framework [NEW]

## Track Overview

Implement the requirement that all importable modules must declare /needs and /wants, and capability access requires declaration.

**Parent Epic**: mlld-vr3w

## Design Principle

Modules are opaque - when you import them, you can't see their internals. The /needs and /wants declarations are the **capability contract** that makes the invisible visible.

- `/needs` = Hard requirements (module won't load without these)
- `/wants` = Tiered requirements (graceful degradation)
- Static analysis can auto-generate /needs; declarations are verified

## Beads in This Track

### C1: Require /needs and /wants for module imports
- Module loader must verify declarations exist
- ImportDirectiveEvaluator checks before importing

### C2: Capability gating framework
- Generalize the keychain pattern from A3
- All capabilities (network, filesystem, sh, cmd) require declaration
- Centralized capability checker

### C3: Fix MCP arg stringification
- Object/array inputs become "[object Object]"
- Need structured value preservation

### C4: mlld analyze command
- Static analysis to generate /needs
- Detects keychain, network, filesystem, cmd usage
- Outputs declaration for module author

## Sequence

```
A3 (keychain gate) ──→ C2 (generalized framework)
                              │
C1 (/needs required) ─────────┤
                              ├──→ Capability model complete
C3 (MCP args) ────────────────┤
                              │
C4 (mlld analyze) ────────────┘
```

C1 and C3 can run in parallel. C2 builds on A3 pattern. C4 can start anytime.

## Key Files

```
interpreter/eval/import/ImportDirectiveEvaluator.ts
core/registry/ModuleLoader.ts
cli/mcp/FunctionRouter.ts:107
```

## Exit Criteria

- [ ] Modules without /needs and /wants cannot be imported
- [ ] All capability access requires declaration
- [ ] MCP preserves structured arg values
- [ ] `mlld analyze` outputs /needs declaration
- [ ] Tests verify enforcement

## Estimated Effort
15-23 hours total


