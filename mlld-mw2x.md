---
id: mlld-mw2x
status: open
deps: [mlld-nop1]
links: []
created: 2026-01-22T16:28:12.969945-08:00
type: task
priority: 1
parent: mlld-4z2w
---
# Phase 1b: Exe parameter type annotations

## Goal

Add type annotations to exe parameters so MCP schemas have proper types instead of all-string.

## Context

Currently all parameters typed as string:
```typescript
// cli/mcp/SchemaGenerator.ts:44
properties[param] = { type: 'string' };  // Always string
```

## Syntax

```mlld
>> Typed parameters
exe @getIssue(id: number, includeComments: boolean) = [...]

>> With defaults (stretch)
exe @search(query: string, limit: number = 10) = [...]
```

## Integration Points

1. **Grammar**: `grammar/mlld.pegjs` - Param syntax: `name: type` or `name: type = default`
2. **AST Types**: `core/types/ast.ts` - ParamDefinition with optional type/default
3. **Interpreter**: `interpreter/eval/exe-definition.ts` - Store param types
4. **Types**: `core/types/executable.ts` - paramTypes field on ExecutableVariable
5. **Schema**: `cli/mcp/SchemaGenerator.ts` - Map mlld types to JSON Schema types

## Type Mapping

| mlld type | JSON Schema |
|-----------|-------------|
| string | string |
| number | number |
| boolean | boolean |
| array | array |
| object | object |
| (untyped) | string (default) |

## Exit Criteria

- [ ] `exe @foo(x: number)` parses with type info
- [ ] ExecutableVariable stores param types
- [ ] SchemaGenerator emits correct JSON Schema types
- [ ] Test: typed params appear correctly in tools/list

## Estimated Effort
~3 hours

## Parent Epic
mlld-4z2w

## Depends On
mlld-nop1 (exe description support)


