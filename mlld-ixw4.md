---
id: mlld-ixw4
status: open
deps: [mlld-9uju]
links: []
created: 2026-01-22T16:28:50.764346-08:00
type: task
priority: 3
parent: mlld-4z2w
---
# Phase 2b: Environment tool scoping

## Goal

Integrate tool collections with environments so agents see only permitted tools.

## Context

Environments control what tools are available to an agent. Three patterns:

```mlld
>> Pattern A: new extending base
var @devEnv = new @baseEnv with { tools: @fullTools }

>> Pattern B: Env as function
exe @agentEnv(tools) = { auth: "claude", tools: @tools }
env @agentEnv(@devTools) [...]

>> Pattern C: Inline derivation  
env @baseEnv with { tools: [@readFile] } [...]
```

## Integration Points

1. **Env Config**: `core/types/env-config.ts` - Add `tools` field to environment config
2. **Env Derivation**: `interpreter/eval/env-block.ts` - Handle `with { tools }`
3. **Tool Filtering**: When serving MCP, filter to env's tool set
4. **Attenuation**: Child envs can only subset parent tools (security invariant)
5. **FunctionRouter**: `cli/mcp/FunctionRouter.ts` - Check tool allowed before routing

## Attenuation Invariant

```
parent.tools = [a, b, c, d]
child.tools = [a, b]  // OK - subset
child.tools = [a, b, e]  // ERROR - can't add 'e'
```

## Flow

```
env @config with { tools: @subset } [
  run cmd { claude -p @task }
]
    │
    ▼
MCPServer.tools/list
    │
    ▼
Return ONLY tools in @subset (filtered)
    │
    ▼
MCPServer.tools/call
    │
    ▼
FunctionRouter checks: is tool in @subset?
    │
    ├─ Yes → execute (with labels from tool def)
    └─ No → error "Tool not available"
```

## Exit Criteria

- [ ] Env config accepts `tools` field
- [ ] `new @env with { tools }` works
- [ ] `env @func(@tools) [...]` works
- [ ] tools/list returns only scoped tools
- [ ] tools/call rejects tools outside scope
- [ ] Attenuation enforced (subset only)
- [ ] Test: scoped env limits available tools

## Estimated Effort
~4 hours

## Parent Epic
mlld-4z2w

## Depends On
mlld-9uju (tool collection syntax)


