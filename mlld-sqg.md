---
id: mlld-sqg
status: open
deps: []
links: []
created: 2025-12-18T10:21:10.33158-08:00
type: feature
priority: 1
tags: [size-l, complexity-l, risk-l, impl-none]
---
# Design: MCP inline server orchestration with /mcp directive

Add inline MCP server orchestration to mlld scripts using /mcp directive.

**Syntax:**
```mlld
/exe @tool() = node { return 'data'; }

/mcp @server = {
  tools: [@tool],
  config: { MLLD_TOKEN: "secret" }
}

/exe @agent(task) = @task | { claude } with { mcp: @server }
```

**Features:**
- /mcp directive defines server config
- with { mcp: @server } spawns server and pipes stdio
- with { mcp: inherit } for nested commands
- Script-scoped lifecycle (spawn on first use, kill at end)

**Implementation plan in GH#495:**
- Day 1: Grammar + types
- Day 2: Directive eval + server manager
- Day 3: Pipeline integration
- Day 4: Testing + docs

**v1a limitations:** One server per command (multi-server requires proxy in v1b)

GitHub issue: #495


