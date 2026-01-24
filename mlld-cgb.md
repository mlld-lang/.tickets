---
id: mlld-cgb
status: closed
deps: [mlld-p25]
links: []
created: 2025-12-09T08:30:21.961653-08:00
type: task
priority: 1
parent: mlld-a03
---
# LSP: update highlighting for strict and markdown modes

## Summary
Update LSP semantic tokens and visitors to correctly highlight strict vs markdown modes.

**Depends on:** mlld-p25 (mode detection) must be completed first.

## Key Difference

**Strict Mode (.mld files):**
- Bare directives (`var`, `show`, `exe`) are valid and should highlight as keywords
- Text lines produce parse errors
- Slash prefix optional (`/var` and `var` both work)

**Markdown Mode (.mld.md files):**
- Only `/directive` syntax highlighted as keywords
- Text lines become content (no error)
- Slash prefix required

## Implementation

### DirectiveVisitor Changes
In `services/lsp/visitors/DirectiveVisitor.ts`, the `visitNode` method currently adds directive tokens assuming slash prefix (line 37-45):

```typescript
// Current code adds token with length = kind.length + 1 (for /)
this.tokenBuilder.addToken({
  line: node.location.start.line - 1,
  char: node.location.start.column - 1,
  length: (node.kind?.length || 0) + 1,  // +1 assumes slash
  tokenType: 'directive',
  modifiers: []
});
```

**Changes needed:**
1. Accept mode in visitor context
2. Check if directive has slash prefix or is bare
3. Adjust token length accordingly

```typescript
// Check if bare directive (no slash) in strict mode
const hasSlash = !node.meta?.bare;  // or check source text
const tokenLength = hasSlash 
  ? (node.kind?.length || 0) + 1 
  : (node.kind?.length || 0);

this.tokenBuilder.addToken({
  line: node.location.start.line - 1,
  char: node.location.start.column - 1,
  length: tokenLength,
  tokenType: 'directive',
  modifiers: []
});
```

### ASTSemanticVisitor Changes
Pass mode through visitor context so DirectiveVisitor can access it:

```typescript
// In ASTSemanticVisitor constructor or visitAST
this.contextStack.push({ mode: options.mode });
```

### Test Coverage
Add tests for both modes:

```typescript
describe('strict mode highlighting', () => {
  it('highlights bare directives', async () => {
    const tokens = await getSemanticTokens('var @x = "test"', { mode: 'strict' });
    expect(tokens).toContainToken({ type: 'keyword', text: 'var' });
  });
});

describe('markdown mode highlighting', () => {
  it('requires slash prefix', async () => {
    const tokens = await getSemanticTokens('/var @x = "test"', { mode: 'markdown' });
    expect(tokens).toContainToken({ type: 'keyword', text: '/var' });
  });
});
```

## Files to Modify
- `services/lsp/visitors/DirectiveVisitor.ts` - Handle bare vs slashed
- `services/lsp/context/VisitorContext.ts` - Add mode to context type
- `services/lsp/ASTSemanticVisitor.ts` - Pass mode to context
- `services/lsp/semantic-tokens.test.ts` - Add mode-specific tests

## Acceptance Criteria
- [ ] Bare directives in strict mode highlighted as keywords
- [ ] Slash directives work in both modes
- [ ] Token lengths correct for both styles
- [ ] Test coverage for both modes


