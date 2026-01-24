---
id: mlld-xiw
status: closed
deps: []
links: []
created: 2025-12-11T20:12:36.61009-08:00
type: bug
priority: 3
parent: mlld-a03
---
# LSP: Fix array/object mlld value highlighting (GH#332)

## Summary
Arrays and objects with mlld values have inconsistent highlighting.

GitHub: https://github.com/mlld-lang/mlld/issues/332

## Problem
```mlld
/var @data = {
  name: @userName,      << @userName not highlighted
  items: [@a, @b, @c]   << Variables not highlighted
}
```

## Root Cause
The StructureVisitor handling objects and arrays doesn't recursively visit mlld value nodes.

## Investigation
```bash
npm run ast -- '/var @data = { name: @userName }'
# Check if @userName is a VariableReference node with location
```

## Implementation

### Check StructureVisitor
In `services/lsp/visitors/StructureVisitor.ts`:

```typescript
visitObjectExpression(node: any, context: VisitorContext): void {
  // Tokenize opening brace
  // ...
  
  // Process properties
  if (node.properties) {
    for (const prop of node.properties) {
      this.visitProperty(prop, context);
    }
  }
  
  // Tokenize closing brace
}

visitProperty(node: any, context: VisitorContext): void {
  // Tokenize property key
  if (node.key) {
    this.tokenBuilder.addToken({
      line: node.key.location.start.line - 1,
      char: node.key.location.start.column - 1,
      length: node.key.name.length,
      tokenType: 'property',
      modifiers: []
    });
  }
  
  // Tokenize colon
  // ...
  
  // IMPORTANT: Recursively visit the value
  if (node.value) {
    this.mainVisitor.visitNode(node.value, context);
  }
}

visitArrayExpression(node: any, context: VisitorContext): void {
  // Tokenize opening bracket
  // ...
  
  // IMPORTANT: Recursively visit elements
  if (node.elements) {
    for (const element of node.elements) {
      this.mainVisitor.visitNode(element, context);
    }
  }
  
  // Tokenize closing bracket
}
```

The key is ensuring `mainVisitor.visitNode()` is called for values/elements so VariableReferences get proper tokens.

## Testing
```typescript
it('highlights variables in object values', async () => {
  const tokens = await getSemanticTokens('/var @x = { name: @value }');
  expect(tokens).toContainToken({ type: 'variable', text: '@value' });
});

it('highlights variables in array elements', async () => {
  const tokens = await getSemanticTokens('/var @x = [@a, @b]');
  expect(tokens).toContainToken({ type: 'variable', text: '@a' });
  expect(tokens).toContainToken({ type: 'variable', text: '@b' });
});
```

## Files to Modify
- `services/lsp/visitors/StructureVisitor.ts`

## Size
Small - ensure recursive visitation of values/elements


