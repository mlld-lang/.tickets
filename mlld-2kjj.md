---
id: mlld-2kjj
status: closed
deps: []
links: []
created: 2026-01-15T10:55:17.32995-08:00
type: task
priority: 2
tags: [size-s, complexity-s, risk-s, impl-none]
parent: mlld-urik
updated: 2026-01-30T06:41:55Z
---
# D1-GAP: Create missing helper modules

## Problem

Only @mlld/env exists. Missing:
- @mlld/mcp
- @mlld/keychain  
- @mlld/env-security

## Current State

```
registry/modules/mlld/
├── ai-cli/
├── array/
├── claude/
├── env/          ✅ EXISTS
├── github/
├── prose/
├── stream-claude-agent-sdk/
└── string/
```

## Modules to Create

### 1. @mlld/mcp

MCP configuration utilities:
```mlld
/needs { }
/wants [ ]

/exe @mcpMerge(...configs) = js {
  const servers = [];
  for (const config of configs.filter(Boolean)) {
    servers.push(...(config.servers || []));
  }
  return { servers };
}

/exe @mcpServer(opts) = { servers: [@opts] }

/export { @mcpMerge, @mcpServer }
```

### 2. @mlld/keychain

Keychain convenience wrappers:
```mlld
/needs { keychain }
/wants [ ]

/import { get, set, delete } from @keychain

/exe @envToken(name) = get("mlld-env", @name)
/exe @setEnvToken(name, token) = set("mlld-env", @name, @token)

/export { @envToken, @setEnvToken }
```

### 3. @mlld/env-security

Standard security guards for environments:
```mlld
/needs { }
/wants [ ]

/guard @tokenProtection before secret = when [
  @mx.op.type == "show" => deny "Tokens cannot be displayed"
  @mx.op.type == "output" => deny "Tokens cannot be written to files"
  * => allow
]

/export { @tokenProtection }
```

## File Structure

Each module needs:
```
registry/modules/mlld/<name>/
├── module.yml
├── index.mld
└── 1.0.0.json (version manifest)
```

## Exit Criteria

- [ ] @mlld/mcp exists with @mcpMerge, @mcpServer exports
- [ ] @mlld/keychain exists with @envToken, @setEnvToken exports
- [ ] @mlld/env-security exists with @tokenProtection guard
- [ ] All modules have /needs and /wants declarations
- [ ] All modules parse and evaluate correctly

## Estimated Effort
4-6 hours


