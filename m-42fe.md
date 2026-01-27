---
id: m-42fe
status: closed
deps: [mlld-75yv]
links: []
created: 2026-01-24T04:45:22Z
type: task
priority: 3
assignee: Adam Avenir
parent: mlld-4z2w
---
# Phase 3b-1: MCP import name collision rules

## Goal

Define deterministic resolution for tool name collisions when importing from MCP.

## Context

```mlld
exe @readFile(path) = [...]  // Local exe

import tools { @readFile } from mcp "@anthropic/filesystem"
// Collision: local @readFile vs imported @readFile
```

## Options

### Option A: Require prefix (recommended)
```mlld
import tools from mcp "@anthropic/filesystem" as @fs
// Usage: @fs.readFile(...)
```

### Option B: Local wins, warn
```mlld
import tools { @readFile } from mcp "..."
// Warning: @readFile shadows MCP import
```

### Option C: Error on collision
```mlld
// Error: @readFile already defined
```

## Recommendation

Option A as default. Unprefixed import only allowed if no collision.

## Exit Criteria

- [ ] Collision detection implemented
- [ ] Prefix syntax works (`as @prefix`)
- [ ] Clear error message on collision without prefix
- [ ] Test: collision scenarios

## Estimated Effort
~2 hours

## Depends On
mlld-75yv (import tools from MCP syntax)

