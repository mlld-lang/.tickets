---
id: mlld-a09
status: closed
deps: []
links: []
created: 2025-12-11T20:12:37.357221-08:00
type: bug
priority: 3
parent: mlld-a03
---
# LSP: Fix when block assignment highlighting (GH#327)

## Summary
Explicit assignment in when blocks shows 'Other' type instead of proper highlighting.

GitHub: https://github.com/mlld-lang/mlld/issues/327

## Problem
When using explicit variable assignment in when blocks, the assignment doesn't get highlighted:

```mlld
/when @condition [
  var @result = "value"  << 'var' and '@result' show as 'Other'
]
```

## Root Cause
The DirectiveVisitor's when block handling doesn't process nested `var` declarations inside block bodies.

## Implementation

### Locate the Issue
In `services/lsp/visitors/DirectiveVisitor.ts`, find `visitWhenDirective` method.

The when block body contains statements that need to be visited. Check if the body is being recursively visited:

```typescript
// In visitWhenDirective, ensure body statements are visited
if (node.values?.body && Array.isArray(node.values.body)) {
  for (const stmt of node.values.body) {
    this.mainVisitor.visitNode(stmt, context);
  }
}
```

### Fix Approach
1. Ensure when block bodies are recursively visited
2. Var/let statements inside blocks should be processed as directives
3. Variable declarations should get 'variable' token with 'declaration' modifier

## Testing
```bash
npm run ast -- '/when @x [var @y = 1]'
# Check AST structure to understand node types

npm test services/lsp/semantic-tokens.test.ts
```

## Files to Modify
- `services/lsp/visitors/DirectiveVisitor.ts` - visitWhenDirective method

## Size
Small - likely a few lines to add recursive visitation


