---
id: mlld-9uju
status: open
deps: [mlld-mw2x]
links: []
created: 2026-01-22T16:28:30.508271-08:00
type: task
priority: 1
parent: mlld-4z2w
---
# Phase 2a: Tool collection syntax (var tools)

## Goal

Implement `var tools @name = {...}` syntax for declarative tool bundles.

## Context

Tool collections define which tools are available to an agent. Each tool in the collection can:
- Reference a native mlld exe
- Have labels (destructive, net:w, etc.)
- Have description overrides
- Be reshaped (bind params, expose subset)

## Syntax

```mlld
var tools @agentTools = {
  >> Native mlld exe reference
  analyze: { 
    mlld: @analyzeCode 
  },
  
  >> With labels for guard/policy
  deleteRepo: {
    mlld: @deleteRepository,
    labels: [destructive, net:w]
  },
  
  >> Reshaped (curried) tool
  createIssue: {
    mlld: @createGithubIssue,
    bind: { owner: "myorg", repo: "myrepo" },
    expose: ["title", "body"],
    description: "Create issue in myorg/myrepo"
  }
}
```

## Integration Points

1. **Grammar**: `grammar/mlld.pegjs` - `tools` as label on VarDefinition (like `secret`, `untrusted`)
2. **AST Types**: `core/types/ast.ts` - ToolCollectionValue type
3. **Interpreter**: `interpreter/eval/var-definition.ts` - Handle tools-labeled vars
4. **Types**: New `core/types/tools.ts` - ToolCollection, ToolDefinition interfaces
5. **Validation**: Verify mlld references resolve to ExecutableVariables

## Data Structures

```typescript
interface ToolDefinition {
  mlld?: string;           // @exeName reference
  labels?: string[];       // [destructive, net:w]
  description?: string;    // Override exe description
  bind?: Record<string, unknown>;  // Curried params
  expose?: string[];       // Param subset to expose
}

interface ToolCollection {
  [toolName: string]: ToolDefinition;
}
```

## Exit Criteria

- [ ] `var tools @x = {...}` parses
- [ ] Tool definitions validated (mlld refs resolve)
- [ ] ToolCollection type created and populated
- [ ] Test: create tool collection, inspect structure

## Estimated Effort
~4 hours

## Parent Epic
mlld-4z2w

## Depends On
mlld-mw2x (type annotations - for schema generation)


