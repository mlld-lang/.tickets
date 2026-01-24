---
id: mlld-nlu.8
status: closed
deps: [mlld-nlu.7]
links: []
created: 2025-12-11T22:12:02.3358-08:00
type: task
priority: 2
parent: mlld-nlu
---
# mlld-nlu P7: Tests and fixtures

## Tests and Fixtures

### Test File Location
Add tests to `services/lsp/` following existing patterns:
- `services/lsp/chunk-parsing.test.ts` - Unit tests for chunk logic
- Or extend `services/lsp/semantic-tokens.test.ts` with error recovery tests

### Unit Tests for Chunk Splitter

```typescript
describe('splitIntoChunks', () => {
  it('splits on blank lines between directives', () => {
    const text = `/var @a = 1

/var @b = 2`;
    const chunks = splitIntoChunks(text, 'markdown');
    expect(chunks).toHaveLength(2);
  });

  it('does not split inside block syntax', () => {
    const text = `/exe @f() = [
  let @x = 1
  
  let @y = 2
]`;
    const chunks = splitIntoChunks(text, 'markdown');
    expect(chunks).toHaveLength(1);
  });

  it('does not split inside multi-line template', () => {
    const text = `/var @msg = \`
line 1

line 2
\``;
    const chunks = splitIntoChunks(text, 'markdown');
    expect(chunks).toHaveLength(1);
  });

  it('handles strict mode bare directives', () => {
    const text = `var @a = 1

var @b = 2`;
    const chunks = splitIntoChunks(text, 'strict');
    expect(chunks).toHaveLength(2);
  });

  it('caps chunk count at 200', () => {
    const text = Array(300).fill('/var @x = 1\n\n').join('');
    const chunks = splitIntoChunks(text, 'markdown');
    expect(chunks.length).toBeLessThanOrEqual(200);
  });
});
```

### Unit Tests for Location Rebasing

```typescript
describe('rebaseAst', () => {
  it('rebases line numbers', () => {
    const ast = [{ location: { start: { line: 1 }, end: { line: 1 } } }];
    const chunk = { startLine: 10, startOffset: 100 };
    const rebased = rebaseAst(ast, chunk);
    expect(rebased[0].location.start.line).toBe(11);
  });

  it('rebases offsets', () => {
    const ast = [{ location: { start: { offset: 0 }, end: { offset: 10 } } }];
    const chunk = { startLine: 0, startOffset: 50 };
    const rebased = rebaseAst(ast, chunk);
    expect(rebased[0].location.start.offset).toBe(50);
    expect(rebased[0].location.end.offset).toBe(60);
  });

  it('does not mutate original ast', () => {
    const ast = [{ location: { start: { line: 1 } } }];
    rebaseAst(ast, { startLine: 10 });
    expect(ast[0].location.start.line).toBe(1);
  });
});
```

### Integration Tests (Error Recovery)

```typescript
describe('error recovery', () => {
  it('recovers tokens after syntax error', async () => {
    const text = `/var @a = 1
/var @b = INVALID SYNTAX HERE
/var @c = 3`;
    
    const tokens = await getSemanticTokens(text);
    
    // Should have tokens for @a and @c, error for middle line
    expect(tokens.some(t => t.text === '@a')).toBe(true);
    expect(tokens.some(t => t.text === '@c')).toBe(true);
  });

  it('recovers tokens before syntax error', async () => {
    const text = `/var @good = 1
BROKEN SYNTAX`;
    
    const tokens = await getSemanticTokens(text);
    expect(tokens.some(t => t.text === '@good')).toBe(true);
  });

  it('handles error inside block without losing surrounding code', async () => {
    const text = `/var @before = 1

/exe @f() = [
  let @x = BROKEN
]

/var @after = 2`;
    
    const tokens = await getSemanticTokens(text);
    expect(tokens.some(t => t.text === '@before')).toBe(true);
    expect(tokens.some(t => t.text === '@after')).toBe(true);
  });
});
```

### Test Fixtures
Create fixture files in `tests/fixtures/lsp/`:
- `error-recovery-middle.mld` - Error in middle of document
- `error-recovery-block.mld` - Error inside block
- `error-recovery-multiple.mld` - Multiple errors

### Deliverable
- [ ] Unit tests for splitIntoChunks
- [ ] Unit tests for rebaseAst
- [ ] Integration tests for error recovery
- [ ] Test strict mode splitting
- [ ] Test chunk count cap


