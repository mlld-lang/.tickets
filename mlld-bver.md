---
id: mlld-bver
status: open
deps: []
links: []
created: 2026-01-14T20:05:50.464996-08:00
type: task
priority: 2
parent: mlld-su55
---
# D1: Create helper modules (@mlld/*)

## Summary

Create standard helper modules that reduce boilerplate for environment module authors.

## Modules to Create

### 1. @mlld/mcp

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

/var @mcpFilter = {
  readonly: ["list_*", "get_*", "search_*"],
  write: ["create_*", "update_*", "delete_*"]
}

/export { @mcpMerge, @mcpServer, @mcpFilter }
```

### 2. @mlld/keychain

```mlld
/needs { keychain }
/wants [ ]

/import { get, set, delete } from @keychain

/exe @envToken(name) = get("mlld-env", @name)
/exe @setEnvToken(name, token) = set("mlld-env", @name, @token)
/exe @hasEnvToken(name) = js {
  try { return !!get("mlld-env", name); }
  catch { return false; }
}

/export { @envToken, @setEnvToken, @hasEnvToken }
```

### 3. @mlld/env

```mlld
/needs { keychain, filesystem }
/wants [ ]

/import { @envToken } from "@mlld/keychain"

/exe @makeSpawn(opts) = [
  /var secret @token = @envToken(@opts.name)
  /exe @fn(prompt) = {
    @opts.tokenEnvVar=@token
    @opts.configVar=@opts.configDir
    @opts.command @prompt
  }
  => @fn
]

/export { @makeSpawn }
```

### 4. @mlld/env-security

```mlld
/needs { }
/wants [ ]

/guard @tokenProtection before secret = when [
  @mx.op.type == "show" => deny "Tokens cannot be displayed"
  @mx.op.type == "output" => deny "Tokens cannot be written"
  @mx.op.labels.includes("net:w") => deny "Tokens cannot be sent over network"
  * => allow
]

/export { @tokenProtection }
```

## File Structure

```
registry/modules/mlld/
├── mcp/
│   ├── module.yml
│   └── index.mld
├── keychain/
│   ├── module.yml
│   └── index.mld
├── env/
│   ├── module.yml
│   └── index.mld
└── env-security/
    ├── module.yml
    └── index.mld
```

## Exit Criteria

- [ ] All 4 modules created with proper module.yml
- [ ] Modules have /needs and /wants declarations
- [ ] Functions work correctly
- [ ] Tests verify each module

## Estimated Effort
6-10 hours


