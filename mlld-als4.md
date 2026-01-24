---
id: mlld-als4
status: closed
deps: []
links: []
created: 2026-01-14T16:01:01.623866-08:00
type: task
priority: 1
parent: mlld-qs0y
---
# Workstream A: CLI Infrastructure (rename + router)

## Workstream Overview

This workstream handles CLI infrastructure changes needed before environment module commands can be implemented.

**Parent Epic**: mlld-qs0y (MCP, Environment Modules, and Policy Integration)

## Sequence

```
mlld-n7c6 (1.1 Rename env→vars) [1h]
    │
    ▼
mlld-7si8 (6.0 Env command router) [1h]
    │
    ▼
[Unblocks all Phase 6.x commands]
```

## Beads in This Workstream

1. **mlld-n7c6** - Phase 1.1: Rename mlld env to mlld vars
   - Rename `cli/commands/env.ts` → `cli/commands/vars.ts`
   - Update CommandDispatcher
   - Free up `env` namespace

2. **mlld-7si8** - Phase 6.0: mlld env command router
   - Create new `cli/commands/env.ts` for environment modules
   - Route subcommands: list, capture, spawn, shell

## Key Files

```
cli/commands/env.ts → cli/commands/vars.ts (rename)
cli/commands/env.ts (new - router)
cli/commands/env/*.ts (subcommands)
cli/execution/CommandDispatcher.ts
```

## Context Documents

- `todo/audits/audit-cli-env.md` - Current env command analysis
- `cli/commands/env.ts` - Current implementation to rename

## Exit Criteria

- [ ] `mlld vars list` works (renamed from env)
- [ ] `mlld vars allow/deny` work
- [ ] `mlld env` shows new help for environment modules
- [ ] `mlld env list/capture/spawn/shell` route correctly (even if not implemented)
- [ ] No references to old `mlld env` for vars remain

## Estimated Effort
2 hours total

## After This Workstream

Unblocks Workstream D (Phase 6.x commands) for parallel execution with other streams.


