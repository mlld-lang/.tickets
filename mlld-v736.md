---
id: mlld-v736
status: open
deps: [mlld-ut9i, mlld-bniq]
links: []
created: 2026-01-14T15:22:52.279471-08:00
type: task
priority: 2
tags: [stale-check-7]
---
# Phase 8: Helper modules (@mlld/mcp, @mlld/keychain, @mlld/env)

## Summary
Create standard helper modules for environment module authors.

## Context
These modules provide common patterns to reduce boilerplate in environment modules.

## Modules to Create

### 1. @mlld/mcp - MCP Configuration Helpers

Location: `registry/mlld/mcp/`

```mlld
/exe @mcpMerge(...configs) = js {
  // Merge multiple MCP configurations
  const servers = [];
  for (const config of configs.filter(Boolean)) {
    servers.push(...(config.servers || []));
  }
  return { servers };
}

/exe @mcpServer(opts) = { servers: [@opts] }

/var @mcpFilter = {
  readonly: ["list_*", "get_*", "search_*"],
  write: ["create_*", "update_*", "delete_*"],
  all: "*"
}

/export { @mcpMerge, @mcpServer, @mcpFilter }
```

### 2. @mlld/keychain - Keychain Helpers

Location: `registry/mlld/keychain/`

```mlld
/needs { keychain }

/exe @envToken(name) = keychain.get("mlld-env", @name)

/exe @setEnvToken(name, token) = keychain.set("mlld-env", @name, @token)

/exe @hasEnvToken(name) = js {
  try {
    const token = keychain.get("mlld-env", name);
    return token && token.length > 0;
  } catch { return false; }
}

/export { @envToken, @setEnvToken, @hasEnvToken }
```

### 3. @mlld/env - Environment Module Helpers

Location: `registry/mlld/env/`

```mlld
/needs { keychain, filesystem }

/import { @envToken } from "@mlld/keychain"

/exe @makeSpawn(opts) = [
  /var secret @token = @envToken(@opts.name)
  /exe @spawn(prompt) = {
    @opts.tokenEnvVar || "OAUTH_TOKEN"=@token
    @opts.configEnvVar || "CONFIG_DIR"=@opts.configDir
    @opts.command @prompt
  }
  => @spawn
]

/exe @tierAwareMcp(tiers) = [
  /exe @mcpConfig() = when [
    @tiers[@mx.policy.tier] => @tiers[@mx.policy.tier]
    @tiers["default"] => @tiers["default"]
    * => { servers: [] }
  ]
  => @mcpConfig
]

/export { @makeSpawn, @tierAwareMcp }
```

### 4. @mlld/env-security - Standard Security Policy

Location: `registry/mlld/env-security/`

```mlld
/guard @tokenProtection before secret = when [
  @mx.op.type == "show" => deny "Tokens cannot be displayed"
  @mx.op.type == "output" => deny "Tokens cannot be written to files"
  @mx.op.labels.includes("net:w") => deny "Tokens cannot be sent over network"
  * => allow
]

/export { @tokenProtection }
```

## Exit Criteria
- [ ] All four modules created with module.yml
- [ ] Modules parse and evaluate correctly
- [ ] @mcpMerge combines server configs
- [ ] @envToken retrieves from keychain
- [ ] @makeSpawn creates spawn function
- [ ] @tierAwareMcp adapts to policy tier
- [ ] @tokenProtection guards work

## Tests to Add
- Unit tests for each helper function
- Integration test with sample environment module

## Dependencies
- Phase 4 (keychain) 
- Phase 5 (environment module type)

## Estimated Effort
10 hours


