---
id: mlld-4bf
status: closed
deps: []
links: []
created: 2025-12-11T20:12:36.760769-08:00
type: bug
priority: 3
parent: mlld-a03
---
# LSP: Highlight function execution in /run directive (GH#331)

## Summary
Function execution in /run directive not highlighted.

GitHub: https://github.com/mlld-lang/mlld/issues/331

## Problem
```mlld
/run @processor(@data)  << @processor not highlighted as function call
```

## Root Cause
The /run directive with exe invocation (calling a defined function) isn't being tokenized properly.

## Investigation
```bash
npm run ast -- '/run @processor(@data)'
# Check the AST structure - is it CommandReference, ExecInvocation, or something else?
```

## Analysis
When /run uses an @function() call, it should be parsed as an ExecInvocation. The CommandVisitor should handle this but may be missing the case.

## Implementation

### Check CommandVisitor
In `services/lsp/visitors/CommandVisitor.ts`:

```typescript
// Ensure ExecInvocation case is handled
visitNode(node: any, context: VisitorContext): void {
  if (node.type === 'ExecInvocation') {
    this.visitExecInvocation(node, context);
    return;
  }
  // ... other cases
}

visitExecInvocation(node: any, context: VisitorContext): void {
  // Token for @functionName
  if (node.name) {
    this.tokenBuilder.addToken({
      line: node.location.start.line - 1,
      char: node.location.start.column - 1,
      length: node.name.length + 1, // +1 for @
      tokenType: 'variable',
      modifiers: ['reference']
    });
  }
  
  // Tokenize parentheses and arguments
  // ...
}
```

### Check DirectiveVisitor /run Handling
The DirectiveVisitor may need to delegate to CommandVisitor for the RHS:

```typescript
// In visitRunDirective or similar
if (node.values?.command?.type === 'ExecInvocation') {
  this.mainVisitor.visitNode(node.values.command, context);
}
```

## Testing
```typescript
it('highlights function calls in /run', async () => {
  const tokens = await getSemanticTokens('/run @processor(@data)');
  expect(tokens).toContainToken({ type: 'variable', text: '@processor' });
  expect(tokens).toContainToken({ type: 'variable', text: '@data' });
});
```

## Files to Modify
- `services/lsp/visitors/CommandVisitor.ts`
- `services/lsp/visitors/DirectiveVisitor.ts`

## Size
Small


