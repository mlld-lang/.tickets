---
id: mlld-4p5
status: closed
deps: []
links: []
created: 2025-12-11T20:12:37.063973-08:00
type: bug
priority: 3
parent: mlld-a03
---
# LSP: Highlight end-of-line comments in when expressions (GH#329)

## Summary
End-of-line comments not highlighted in when expressions.

GitHub: https://github.com/mlld-lang/mlld/issues/329

## Problem
```mlld
/when @x > 0 => "positive"  << This comment not highlighted
     @x < 0 => "negative"   << Nor this one
```

## Root Cause
The ExpressionVisitor or DirectiveVisitor handling when expressions doesn't process trailing comments.

## Investigation
```bash
npm run ast -- '/when @x > 0 => "yes" << comment'
# Check if comment appears in AST and has location
```

Comments in mlld use `>>` (start of line) and `<<` (end of line) syntax. The CommentTokenHelper exists but may not be called for when expression arms.

## Implementation

### Check CommentTokenHelper Usage
In `services/lsp/utils/CommentTokenHelper.ts`, there should be methods for tokenizing comments.

### Add to When Expression Handling
In DirectiveVisitor's `visitWhenDirective` or ExpressionVisitor's when handling:

```typescript
// After processing each when arm, check for trailing comment
if (arm.comment) {
  this.commentHelper.tokenizeComment(arm.comment);
}

// Or if comments are separate nodes in AST:
if (arm.trailingComment) {
  this.mainVisitor.visitNode(arm.trailingComment, context);
}
```

### Verify Comment AST Structure
Comments may be:
1. Attached to the when arm node
2. Separate sibling nodes
3. Part of a comments array

## Testing
```typescript
it('highlights end-of-line comments in when', async () => {
  const tokens = await getSemanticTokens('/when @x => "yes" << comment');
  expect(tokens).toContainToken({ type: 'comment', text: '<< comment' });
});
```

## Files to Modify
- `services/lsp/visitors/DirectiveVisitor.ts` - When handling
- `services/lsp/visitors/ExpressionVisitor.ts` - WhenExpression handling

## Size
Small


