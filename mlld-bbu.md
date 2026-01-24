---
id: mlld-bbu
status: closed
deps: []
links: []
created: 2025-12-11T20:12:36.916573-08:00
type: bug
priority: 3
parent: mlld-a03
---
# LSP: Highlight variable interpolation in /run blocks (GH#330)

## Summary
Variable interpolation not highlighted in /run command blocks.

GitHub: https://github.com/mlld-lang/mlld/issues/330

## Problem
```mlld
/run cmd { echo @message }  << @message not highlighted as variable
/run sh { curl @url }       << @url not highlighted
```

## Root Cause
The CommandVisitor or embedded code handling doesn't process variable references inside command blocks.

## Investigation
```bash
npm run ast -- '/run cmd { echo @message }'
# Check if @message appears as VariableReference in AST
# Or if it's captured as raw string content
```

## Analysis
Command blocks `{ ... }` may be parsed as:
1. Raw string content (no interpolation tracking)
2. Template with interpolation nodes

If it's raw string content, the grammar would need to track interpolations for the LSP to highlight them.

If interpolations ARE in the AST, then CommandVisitor needs to visit them.

## Implementation

### If Interpolations in AST
In `services/lsp/visitors/CommandVisitor.ts`:

```typescript
// When processing command block content
if (node.content?.parts) {
  for (const part of node.content.parts) {
    if (part.type === 'VariableReference') {
      this.mainVisitor.visitNode(part, context);
    }
  }
}
```

### If Raw String Content
This may require grammar changes to preserve interpolation locations, which is a larger change.

Check the AST structure first to determine the approach.

## Testing
```typescript
it('highlights variables in command blocks', async () => {
  const tokens = await getSemanticTokens('/run cmd { echo @message }');
  expect(tokens).toContainToken({ type: 'variable', text: '@message' });
});
```

## Files to Modify
- `services/lsp/visitors/CommandVisitor.ts`

## Size
Small to Medium (depending on AST structure)


