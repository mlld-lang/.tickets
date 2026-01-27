---
id: mlld-8o14
status: closed
deps: []
links: []
created: 2026-01-20T16:24:50.79733-08:00
type: task
priority: 0
---
# MCP Tool Gateway Architecture

## Vision

mlld acts as the **single chokepoint** for all MCP tool calls. This enables:
- Guards and policies to control tool access
- Tool attenuation (subset per agent/env)
- Tool reshaping (currying, renaming, hiding)
- Full observability (@mx.tools.*)
- Unified model for native mlld + external MCP tools

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│  LLM Agent                                                      │
│    "I want to call create_issue(title, body)"                   │
└─────────────────────────┬───────────────────────────────────────┘
                          │ MCP protocol
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│  mlld MCP Gateway (THE chokepoint)                              │
│                                                                 │
│  tools/list →  Returns ONLY what mlld decides to expose         │
│                - Can hide tools from upstream MCP               │
│                - Can rename/reshape tools                       │
│                - Merges native mlld + proxied MCP               │
│                                                                 │
│  tools/call →  ALL calls flow through mlld                      │
│                │                                                │
│                ├─ Guards run (can deny, transform)              │
│                ├─ Audit logging                                 │
│                ├─ Pre-fill bound params (currying)              │
│                ├─ @mx.tools.calls populated                     │
│                │                                                │
│                └─ Route to:                                     │
│                     ├─ Native mlld exe function                 │
│                     └─ Upstream MCP server (proxied)            │
└─────────────────────────────────────────────────────────────────┘
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
    mlld exe        @github/issues    @anthropic/fs
    functions       MCP server        MCP server
```

## Capabilities

| Capability | Example |
|------------|---------|
| **Subset** | Upstream has 20 tools, agent sees 3 |
| **Reshape** | `create_issue(owner, repo, title, body)` → `create_issue(title, body)` with owner/repo pre-bound |
| **Rename** | `dangerous_delete` → not exposed, or renamed to `archive` |
| **Guard** | `@mx.taint.includes("src:mcp")` blocks certain patterns |
| **Audit** | Every call logged with args, result, timing |
| **Observe** | `@mx.tools.calls` shows what agent actually called |

## Current State

**What exists:**
- `cli/mcp/FunctionRouter.ts` - routes MCP calls through standard eval pipeline
- `cli/mcp/SchemaGenerator.ts` - generates basic schemas from exe params
- Guards already work on MCP calls (test proves it)
- `src:mcp` taint auto-applied

**What's missing:**
- Rich tool metadata (descriptions, typed schemas)
- Consuming external MCP servers as mlld tools
- Tool bundles / currying / reshaping
- Declarative tool configuration in mlld code

## Proposed Syntax

### Tool Definition
```mlld
var tool @agentTools = {
  // Native mlld function with metadata
  analyze: {
    mlld: @analyzeCode,
    description: "Analyze code for issues",
    schema: {
      code: { type: "string", description: "Code to analyze" }
    }
  },

  // Proxied MCP, full passthrough
  read_file: {
    mcp: "@anthropic/filesystem::read_file"
  },

  // Proxied MCP, reshaped (curried)
  create_issue: {
    mcp: "@github/issues::create_issue",
    bind: { owner: "myorg", repo: "myrepo" },
    expose: ["title", "body"],
    description: "Create issue in myorg/myrepo"
  }
}
```

### Environment Integration
```mlld
env @agent with { tools: @agentTools } [
  run cmd { claude -p @task }
]
```

### Import Syntax (stretch goal)
```mlld
import tools { @readFile, @writeFile } from mcp "@anthropic/filesystem"
```

## Design Questions

1. **Tool definition semantics** - Is `var tool @x = {...}` the right syntax? Or `tools @x = {...}` as a directive?

2. **Schema inference** - Can we infer schemas from exe function signatures? How do we augment with descriptions?

3. **MCP server lifecycle** - How do we spawn/manage external MCP servers? Connection pooling?

4. **Tool scoping** - How do tools integrate with environments? Per-env tool sets?

5. **Guard integration** - Do existing guards "just work" or do we need tool-specific guard patterns?

6. **@mcpConfig() pattern** - How does this relate to the spec's `@mcpConfig()` function?

7. **Import syntax** - Can we import tools from MCP servers? What's the grammar?

## Unblocks

- **si08.7 (.mx.tools)** - Becomes trivial once FunctionRouter is the chokepoint
- **h47o (env blocks)** - Tool scoping per environment
- **Tool guards** - Already work, just need exposure

## References

- spec-security-2026-v4.md Part 6 (MCP Integration)
- cli/mcp/FunctionRouter.ts (existing implementation)
- cli/mcp/MCPServer.ts (MCP protocol handling)
- mlld-ohya (MCP server lifecycle bug)



