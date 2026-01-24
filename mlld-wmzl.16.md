---
id: mlld-wmzl.16
status: closed
deps: []
links: []
created: 2026-01-18T03:31:10.439447-08:00
type: task
priority: 1
parent: mlld-wmzl
---
# Environment Provider Interface: Add @create with envName

## Goal

Refactor the environment provider interface to separate creation from execution, enabling stateful env blocks with multiple commands in the same environment.

## Current Interface

```mlld
exe @execute(opts, command) = [...]  // creates + executes in one step
exe @release(handle) = [...]
```

Problem: Each `@execute` does `docker run`, creating a fresh container. Env blocks need multiple commands in the same container.

## Proposed Interface

```mlld
exe @create(opts) = [
  // opts.name - optional, for reuse
  // opts.from - optional, restore from snapshot
  // Returns: { envName, created: bool }
]

exe @execute(envName, command) = [
  // command = { argv, cwd, vars, secrets, stdin? }
  // Returns: { stdout, stderr, exitCode }
]

exe @release(envName) = [...]

// Optional
exe @snapshot(envName, name) = [...]
```

## createOrExists Semantics

`@create` should:
1. If `opts.name` specified and exists → return `{ envName: opts.name, created: false }`
2. If `opts.name` specified and not exists → create with that name, return `{ envName, created: true }`
3. If no name → create anonymous, return `{ envName: <auto-id>, created: true }`

## Docker Implementation

```mlld
exe @create(opts) = node {
  // 1. Check if named container exists: docker ps -a --filter name=<name>
  // 2. If exists and running: return { envName, created: false }
  // 3. If exists but stopped: docker start, return { envName, created: false }
  // 4. If not exists: docker run -d [--name] <image> sleep infinity
  //    Return { envName: containerId, created: true }
}

exe @execute(envName, command) = node {
  // docker exec <envName> sh -lc '<command>'
}

exe @release(envName) = node {
  // docker rm -f <envName>
}
```

## mlld Orchestration

| Mode | Flow |
|------|------|
| Ephemeral (guard) | create → execute → release (automatic) |
| Env block | create at `[` → execute per command → release at `]` |
| Named + keep | create (reuse) → execute → NO release |

## Files to Modify

| File | Change |
|------|--------|
| `interpreter/env/environment-provider.ts` | Call @create first, then @execute(envName, ...) |
| `modules/llm/modules/docker.mld` | Refactor to @create/@execute/@release pattern |
| `core/types/environment.ts` | Update types for new interface |
| `docs/dev/SECURITY.md` | Update provider interface docs |
| `todo/spec-security-2026-v3.md` | Update spec |

## Files to Create

| File | Purpose |
|------|---------|
| `modules/llm/modules/sprites.mld` | Sprites provider (stub or full) |

## Exit Criteria

- [ ] `@create(opts)` returns `{ envName, created }`
- [ ] `@execute(envName, command)` uses existing environment
- [ ] Docker provider uses `docker exec` not `docker run` for @execute
- [ ] createOrExists logic works (reuse named containers)
- [ ] Env blocks work (multiple commands, same container)
- [ ] `@snapshot` optional capability (renamed from @checkpoint)
- [ ] Tests updated
- [ ] Docs updated

## References

- Current docker provider: `modules/llm/modules/docker.mld`
- Spec Part 7: Environment Providers


