---
id: mlld-wmzl.9
status: closed
deps: []
links: []
created: 2026-01-16T07:20:16.509421-08:00
type: task
priority: 2
parent: mlld-wmzl
---
# Phase 9: Environments (Unified Isolation & State)

## Goal

Implement environments as the unifying primitive for execution contexts.

## Environment Config Structure

```mlld
var @env = {
  // Core fields (mlld handles)
  provider: "@mlld/env-docker",  // explicit trust grant
  auth: "claude",                 // resolves from policy.auth
  taint: ["src:env"],             // labels for output
  
  // Convention fields (providers interpret)
  fs: { read: [".:/app"], write: ["/out:/out"] },
  net: { allow: ["api.example.com"] },
  
  // Provider opts (passed through)
  image: "node:18",
  memory: "512m",
}
```

## Guard Flow (Select, Not Execute)

Guards **select** the environment; the run evaluator **applies** it:

```
guard returns: env @config
       ↓
guard-pre-hook returns: { decision: 'env', config: @config }
       ↓
run evaluator sees env decision
       ↓
run evaluator loads provider, builds command, calls provider.@execute
       ↓
mlld applies taint labels to result
```

The guard hook does NOT call @execute directly. It returns a decision that the run evaluator acts on. This avoids double-execution and keeps the normal run path in control.

## Provider Trust Model

The `provider:` field is an **explicit trust grant**:

| Module type | Gets secrets? | Why |
|-------------|---------------|-----|
| Regular import | No | Just code, no privileges |
| `provider:` designation | Yes | User explicitly trusts it |

Providers receive actual secret values. This is intentional - the provider controls execution, so it must be trusted.

## Provider Interface

```mlld
// Required
exe @execute(opts, command) = [
  // opts = config minus core fields
  // command = { argv, cwd, vars, secrets }
  // Returns: { stdout, stderr, exitCode }
]

// Optional capabilities
exe @checkpoint(handle, name) = [...]
exe @release(handle) = [...]

export { @execute, @checkpoint, @release }
```

**Command structure (input):**
```mlld
{
  argv: ["claude", "-p", "..."],
  cwd: "/app",
  vars: { NODE_ENV: "production" },
  secrets: { ANTHROPIC_API_KEY: "sk-xxx..." },
}
```

**Result structure (output):**
```mlld
{
  stdout: "...",
  stderr: "...",
  exitCode: 0,
}
```

## Lifecycle: @release

- **Guard-triggered env**: released after single operation completes
- **Env blocks**: released when block exits (success or error)
- **Error paths**: @release always called in finally block

## Policy Integration

When `provider:` is omitted:
- Commands run locally (no isolation)
- Source label is `src:exec` (normal command output)
- `policy.env.default` can specify a default provider (optional)

## Source Labels

Applied by **mlld core**, not providers:
- `src:env:docker` - from Docker provider
- `src:env:sprites` - from Sprites provider
- Providers cannot lie about their labels

## Implementation Scope

**Core environment primitive (~2-3 days):**
- Add `env` to GuardDecisionType
- Grammar: parse `env @config` in guards
- Run evaluator: handle env decision, load provider, call @execute
- Resolve auth from policy.auth → keychain → actual values
- Apply source labels to output

**Docker provider (~1 week):**
- Implement @execute (wrap in docker run)
- Implement @release (docker stop/rm)
- Handle fs, net, image, vars, secrets

## Files to Create

| File | Purpose |
|------|---------|
| `core/types/environment.ts` | EnvConfig, CommandStructure, ResultStructure types |
| `core/env/docker.mld` | @mlld/env-docker provider |

## Files to Modify

| File | Change |
|------|--------|
| `core/types/guard.ts` | Add `'env'` to GuardDecisionType |
| `grammar/directives/guard.peggy` | Add `env` action parsing |
| `interpreter/eval/run.ts` | Handle env decision from guard |
| `core/types/security.ts` | Add `src:env:*` to protected labels |

## Exit Criteria

- [ ] `env` guard action works (select, not execute)
- [ ] Run evaluator applies env decision correctly
- [ ] Provider trust model implemented
- [ ] @mlld/env-docker provider with @execute and @release
- [ ] Command/result structures defined
- [ ] Source labels applied by core
- [ ] @release called on all exit paths
- [ ] Tests: guard selection, provider execution, label application

## References

- Spec Part 5: Environments (The Unifying Primitive)
- Spec Part 7: Environment Providers (Isolation & State)


