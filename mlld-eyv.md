---
id: mlld-eyv
status: closed
deps: []
links: []
created: 2025-12-11T20:12:37.208122-08:00
type: bug
priority: 3
parent: mlld-a03
---
# LSP: Fix pipe transform highlighting parity bug (GH#328)

## Summary
Pipe transforms only highlight correctly with odd number of pipes in /var directives.

GitHub: https://github.com/mlld-lang/mlld/issues/328

## Problem
```mlld
/var @x = @data | @transform1 | @transform2  << Second pipe not highlighted
/var @y = @data | @transform1                << Works fine (1 pipe)
/var @z = @data | @t1 | @t2 | @t3           << Works (3 pipes = odd)
```

## Root Cause
Likely an off-by-one error or loop issue in pipeline operator tokenization.

## Investigation
1. Check `services/lsp/utils/OperatorTokenHelper.ts` - `tokenizePipelineOperators` method
2. Check DirectiveVisitor's handling of pipeline RHS

```bash
npm run ast -- '/var @x = @data | @t1 | @t2'
# Examine pipeline structure in AST
```

## Implementation
The issue is likely in how we iterate through pipeline stages:

```typescript
// Possible bug pattern:
for (let i = 0; i < stages.length - 1; i++) {
  // Tokenize pipe between stages[i] and stages[i+1]
  // If we're skipping every other, check the loop logic
}
```

Look for:
- Loop bounds issues
- Alternating pattern in tokenization
- Missing pipe operators between certain stage pairs

## Testing
```bash
npm test services/lsp/semantic-tokens.test.ts
```

Add test case:
```typescript
it('highlights all pipe operators in chain', async () => {
  const tokens = await getSemanticTokens('/var @x = @data | @t1 | @t2');
  const pipeTokens = tokens.filter(t => t.text === '|');
  expect(pipeTokens).toHaveLength(2);
});
```

## Files to Modify
- `services/lsp/utils/OperatorTokenHelper.ts` or
- `services/lsp/visitors/DirectiveVisitor.ts`

## Size
Small - likely a loop bounds fix


