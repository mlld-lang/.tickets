---
id: mlld-h4zy
status: open
deps: [mlld-794g]
links: []
created: 2026-01-23T07:51:06.663861-08:00
type: task
priority: 2
parent: mlld-4z2w
---
# Phase 2b-2: Env directive and runtime scoping

## Goal

Implement the `env` directive for runtime tool scoping with full environment blocks.

## Context

This is the **complex half** of environment tool scoping. Requires new grammar, evaluator, and runtime infrastructure.

**Target syntax:**
```mlld
>> Pattern A: Inline derivation
env @baseEnv with { tools: @readOnlyTools } [
  run cmd { claude -p @task }
]

>> Pattern B: Named derivation  
var @sandboxEnv = new @baseEnv with { tools: @subset }
env @sandboxEnv [ ... ]

>> Pattern C: Env as function
exe @agentEnv(tools) = { auth: "claude", tools: @tools }
env @agentEnv(@devTools) [ ... ]
```

## What's Needed

### 1. Grammar
- New `env` directive in `grammar/directives/`
- Syntax: `env @config [ block ]` or `env @config with {...} [ block ]`

### 2. AST Types
- `EnvDirectiveNode` in `core/types/`
- Fields: config reference, withClause, block

### 3. Dispatcher
- Add `case 'env'` to `directive.ts`

### 4. Evaluator
- New `evaluateEnv()` function
- Create scoped environment for block
- Apply tool restrictions
- Execute block statements
- Clean up on exit

### 5. Tool Scoping Runtime
- Environment needs `allowedTools: Set<string>` field
- FunctionRouter checks against allowedTools
- Child environments inherit and can only restrict

## Exit Criteria

- [ ] `env @config [...]` parses
- [ ] `env @config with { tools } [...]` parses
- [ ] Block executes in scoped environment
- [ ] Tool restrictions enforced at runtime
- [ ] Attenuation invariant enforced
- [ ] Test: env block limits tools

## Estimated Effort
~6 hours

## Parent Epic
mlld-4z2w

## Depends On
mlld-ixw4-a (Phase 2b-1: Tool filtering)


