---
id: mlld-h47o
status: open
deps: []
links: []
created: 2026-01-18T11:28:48.429552-08:00
type: task
priority: 2
parent: mlld-si08
---
# Env block syntax and dynamic env selection

## Summary

Add `env @config [...]` block syntax for scoped environment execution, and support dynamic env selection via executable calls.

## Block Syntax

From spec (Part 5.2):
```mlld
env @sandbox [
  run cmd "npm install"
  run cmd "npm test"
]
// Environment released when block exits
```

**Use case:** Run multiple commands in the same container/environment instance. Without blocks, each guard-triggered env is potentially fresh.

## Dynamic Env Selection

From spec (Part 7.4):
```mlld
exe @agentEnv(tools, mcps) = {
  auth: "claude",
  tools: @tools,
  mcps: @mcps,
}

guard before sandboxed = when [
  op:cmd => env @agentEnv(["Read", "Write"], ["@github/issues"])
  * => allow
]
```

**Use case:** Parameterize environments with specific tools/MCPs/skills enabled.

## Grammar Work Needed

1. Add `env` directive for block syntax
2. Verify guard `env` action supports executable calls (not just variable refs)
3. `snapshot "name"` inside env blocks (depends on provider support)

## Ties To

- Policy DSL: env configs reference `policy.auth` for credentials
- Provider interface: blocks need `@create` at start, `@release` at end
- Snapshots: in-block `snapshot` calls `provider.@snapshot()`

## Exit Criteria

- [ ] `env @config [...]` block syntax parses
- [ ] Block maintains single env instance across commands
- [ ] `=> env @exe(@args)` works in guard actions
- [ ] Release called at block end (or on error)

## Priority

P2 - Enhancement. Guard-scoped env selection works for single commands.


