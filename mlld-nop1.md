---
id: mlld-nop1
status: closed
deps: []
links: []
created: 2026-01-22T16:27:57.442205-08:00
type: task
priority: 1
parent: mlld-4z2w
---
# Phase 1a: Exe description support

## Goal

Add `description` field support to exe definitions so MCP tools have meaningful descriptions for LLMs.

## Context

Currently `SchemaGenerator.ts` emits empty descriptions:
```typescript
// cli/mcp/SchemaGenerator.ts:35
description: '',  // Always empty
```

LLMs need descriptions to understand tool purpose. This is the foundation for better tool metadata.

## Syntax

```mlld
>> Option A: with block
exe @analyzeCode(code) = [...] with {
  description: "Analyze code for issues and suggest improvements"
}

>> Option B: annotation (if simpler to parse)
exe @analyzeCode(code) = [...]
  @description "Analyze code for issues"
```

Recommend Option A - `with` blocks already exist in grammar for other purposes.

## Integration Points

1. **Grammar**: `grammar/mlld.pegjs` - Add `with` block to ExeDefinition
2. **AST Types**: `core/types/ast.ts` - Add `meta.description` to ExecutableDefinition
3. **Interpreter**: `interpreter/eval/exe-definition.ts` - Store description in ExecutableVariable
4. **Types**: `core/types/executable.ts` - Add description field to ExecutableVariable
5. **Schema**: `cli/mcp/SchemaGenerator.ts` - Read description from ExecutableVariable

## Exit Criteria

- [ ] `exe @foo() = ... with { description: "..." }` parses
- [ ] Description stored in ExecutableVariable
- [ ] SchemaGenerator emits description in MCPToolSchema
- [ ] Test: exe with description shows in tools/list response

## Estimated Effort
~3 hours

## Parent Epic
mlld-4z2w


