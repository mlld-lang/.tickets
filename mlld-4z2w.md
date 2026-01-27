---
id: mlld-4z2w
status: open
deps: []
links: [m-2af3]
created: 2026-01-22T16:27:39.567852-08:00
type: epic
priority: 1
---
# MCP Tool Gateway Implementation

## Vision

mlld acts as the **single chokepoint** for all MCP tool calls, enabling guards, policies, tool attenuation, reshaping, and full observability.

## Architecture

```
LLM Agent
    │ MCP protocol
    ▼
┌─────────────────────────────────────────────┐
│ mlld MCP Gateway                            │
│   tools/list → mlld-controlled tool set     │
│   tools/call → ALL calls through guards     │
│   - Tool reshaping (curry, rename, hide)    │
│   - Labels on individual tools              │
│   - @mx.tools.* populated                   │
└─────────────────────────────────────────────┘
    │
┌───┴───────────┐
▼               ▼
mlld exes    External MCP (Phase 3)
```

## Key Design Decisions

1. **Tool collections**: `var tools @name = {...}` with inline `labels:` per tool
2. **Env patterns**: `new @base with { tools }`, `@envFunc(tools)`, inline derivation
3. **MCP lifecycle**: mlld exes first (Phase 1-2), external spawning (Phase 3)
4. **Guard routing**: Unified exe-style with origin metadata (`@mx.op.source`)

## Phases

- Phase 1: Tool metadata (descriptions, schemas)
- Phase 2: Tool configuration syntax (`var tools`, reshaping)
- Phase 3: External MCP consumption
- Phase 4: @mx.tools namespace (separate track)

## References

- spec-security-2026-v4.md Part 6 (MCP Integration)
- cli/mcp/*.ts (existing implementation)
- mlld-8o14 (original design card)


