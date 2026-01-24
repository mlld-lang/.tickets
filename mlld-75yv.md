---
id: mlld-75yv
status: open
deps: [mlld-964m]
links: []
created: 2026-01-22T16:34:18.776293-08:00
type: task
priority: 2
parent: mlld-4z2w
---
# Phase 3b: Import tools from MCP syntax

## Goal

Add grammar support for importing tools directly from MCP servers.

## Syntax

```mlld
>> Import specific tools
import tools { @readFile, @writeFile } from mcp "@anthropic/filesystem"

>> Import all tools with prefix
import tools from mcp "@github/issues" as @github

>> Usage
show @github.createIssue("title", "body")
```

## Semantics

1. Spawns/connects to MCP server
2. Calls tools/list to get available tools
3. Creates mlld executables that proxy to server
4. All calls flow through guards (src:mcp taint)

## Integration Points

1. **Grammar**: `grammar/mlld.pegjs` - New import variant
2. **AST Types**: `core/types/ast.ts` - McpToolImport node
3. **Interpreter**: Create proxy ExecutableVariables
4. **Tool Registry**: Track imported tools for routing

## Exit Criteria

- [ ] `import tools {...} from mcp "..."` parses
- [ ] MCP server spawned on import
- [ ] Proxy executables created
- [ ] Calls route through guards
- [ ] Test: import and call external MCP tool

## Estimated Effort
~6 hours

## Parent Epic
mlld-4z2w

## Depends On
mlld-964m (external MCP spawning)


