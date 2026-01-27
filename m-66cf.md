---
id: m-66cf
status: closed
deps: []
links: [m-2af3]
created: 2026-01-24T04:45:23Z
type: task
priority: 2
assignee: Adam Avenir
parent: mlld-4z2w
---
# Epic docs: MCP Tool Gateway documentation

## Goal

Update atom-based documentation for MCP Tool Gateway features so they can be tested via `mlld run qa`.

## Current State Assessment (2026-01-24)

### Documented (atoms exist)

| Feature | Atom | Status |
|---------|------|--------|
| Tool collection syntax | `commands/mcp-tools.md` | Basic coverage |
| Import MCP tools | `commands/mcp-tools.md` | Documented |
| Guards with @mx.op.labels | `security/guards-basics.md` | Documented |
| env with tools (brief) | `commands/mcp-tools.md` | One example only |

### NOT Documented (atoms needed)

| Feature | Spec Section | Priority |
|---------|--------------|----------|
| **Exe metadata** - `with { description }`, typed params | Phase 1a/1b | High |
| **Tool reshaping** - bind/expose with examples | Part 1.5, 2.2 | High |
| **Environment directive** - `env @x [...]` blocks | Part 5 | High |
| **Tool call tracking** - @mx.tools.calls/allowed/denied | Part 1.9 | Medium |
| **Label modification** - `=> label @var`, `=> trusted! @var` | Part 4.3 | Medium |
| **Privileged guards** - `with { privileged: true }` | Part 4.6 | Low |
| **profiles declaration** | Part 2.4 | Low |

## Scope (Updated)

### New Atoms Required

1. **`commands/exe-metadata.md`** (High)
   - `exe @f(x) = ... with { description: "..." }`
   - Typed parameters: `exe @f(x: number, y: string)`
   - qa_tier: 2

2. **`commands/tool-reshaping.md`** (High)
   - bind: pre-filling parameters
   - expose: limiting visible parameters
   - Combined examples
   - qa_tier: 2

3. **`commands/env-directive.md`** (High)
   - `env @config [...]` blocks
   - Tool scoping with `with { tools: @x }`
   - Child derivation: `new @parent with {...}`
   - qa_tier: 2

4. **`security/tool-call-tracking.md`** (Medium)
   - @mx.tools.calls - tool call history
   - @mx.tools.allowed - permitted tools
   - @mx.tools.denied - blocked tools
   - Guard examples using these
   - qa_tier: 2

5. **`security/label-modification.md`** (Medium)
   - Adding labels: `=> label @var`
   - Trust modification: `=> trusted @var`, `=> untrusted @var`
   - Privileged operations: `=> trusted! @var`, `=> !label @var`
   - qa_tier: 2

### Atoms to Update

1. **`commands/mcp-tools.md`** - expand bind/expose examples, add qa_tier
2. **`security/guards-basics.md`** - already has qa_tier: 2, verify coverage

### Build Script Updates

- Update `docs/build/llm/commands.mld` to include new atoms
- Update `docs/build/llm/security.mld` to include new atoms
- Regenerate `docs/llm/llms-commands.txt` and `docs/llm/llms-security.txt`
- Regenerate `llms-combined.txt`

## Exit Criteria

- [ ] All High priority atoms created with qa_tier
- [ ] `mlld run qa --tier 2 --topic exe-metadata` passes
- [ ] `mlld run qa --tier 2 --topic tool-reshaping` passes
- [ ] `mlld run qa --tier 2 --topic env-directive` passes
- [ ] llms-combined.txt regenerated
- [ ] Medium priority atoms created (can be follow-up)

## Notes

The qa.mld script tests atoms by having agents explore them with limited access. Atoms need:
- `qa_tier: 1` or `qa_tier: 2` in frontmatter
- Clear examples that are syntactically valid
- Related code references

## Estimated Effort

~4-5 hours total for High priority items

---

**2026-01-24T19:46:43Z**

Docs updated: MCP tool collections, MCP tool gateway page, guards basics tool labels, llms.txt + llms-commands.txt updates, changelog entries for MCP import/external spawning. Environment scoping doc page not updated yet; keep open for remaining doc scope.

**2026-01-24T23:XX:XXZ**

Comprehensive assessment completed. Identified 5 new atoms needed, 2 atoms to update. Prioritized by importance for qa testing and user comprehension.

**2026-01-25T06:18:49Z**

## Documentation Updates Complete (2026-01-24)

### Atoms Created (High Priority)

1. **exe-metadata.md** - Typed parameters (`exe @f(x: string)`) and `with { description }` metadata
2. **tool-reshaping.md** - bind/expose for tool parameter control  
3. **env-directive.md** - `env @config [...]` blocks and tool scoping

### Atoms Created (Medium Priority)

4. **tool-call-tracking.md** - @mx.tools.calls/allowed/denied namespace
5. **label-modification.md** - `=> label @var`, `=> trusted! @var`, privilege requirements

### Build Script Updates

- Updated `docs/build/llm/commands.mld` with TOOLS_AND_ENVIRONMENTS section
- Updated `docs/build/llm/security.mld` with LABEL_MODIFICATION and TOOL_CALL_TRACKING sections

### LLM Docs Regenerated

- `docs/llm/llms-commands.txt` - now includes tool/env atoms  
- `docs/llm/llms-security.txt` - now includes label modification and tool tracking
- `llms-combined.txt` - regenerated (13 modules, version 2.0.0-rc81-ab)

### Verification

All 5 atoms verified against implementation:
- Syntax examples are valid
- Behavior claims match test cases
- Privilege requirements correctly documented
- All format variations supported
