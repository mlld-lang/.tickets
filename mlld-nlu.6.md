---
id: mlld-nlu.6
status: closed
deps: [mlld-nlu.5]
links: []
created: 2025-12-11T22:11:30.692991-08:00
type: task
priority: 2
parent: mlld-nlu
---
# mlld-nlu P5: LSP integration

## LSP Integration

### Integration Point
In `cli/commands/language-server-impl.ts`, around **line 566**:
```typescript
// Current code:
const partialAst = await attemptPartialParsing(text, error, mode);
ast = partialAst.nodes;
errors.push(...partialAst.errors);
```

### New Flow
```typescript
async function parseWithChunks(
  text: string, 
  originalError: any, 
  mode: MlldMode
): Promise<{ nodes: any[], errors: Diagnostic[], failedRanges: Range[] }> {
  
  // Fast path: if chunk parsing disabled, use old method
  if (process.env.MLLD_CHUNK_PARSING === '0') {
    const result = await attemptPartialParsing(text, originalError, mode);
    return { ...result, failedRanges: [] };
  }
  
  // Split into chunks
  const chunks = splitIntoChunks(text, mode);
  
  // Safety: if too many chunks, fall back to single-chunk
  if (chunks.length > 200) {
    logger.warn('Too many chunks, falling back to single chunk');
    chunks.splice(0, chunks.length, { text, startLine: 0, startOffset: 0, ... });
  }
  
  // Parse each chunk
  const results = await Promise.all(
    chunks.map(chunk => parseChunk(chunk, mode))
  );
  
  // Merge results
  const merged = mergeChunkResults(results);
  
  // Final fallback: if all chunks failed, try old strategy
  if (merged.nodes.length === 0 && results.every(r => !r.success)) {
    logger.debug('All chunks failed, trying legacy partial parsing');
    const legacy = await attemptPartialParsing(text, originalError, mode);
    return { ...legacy, failedRanges: [] };
  }
  
  return merged;
}
```

### Wiring into analyzeDocument
```typescript
// Replace line ~566:
const partialAst = await parseWithChunks(text, error, mode);
ast = partialAst.nodes;
errors.push(...partialAst.errors);

// Optionally store failedRanges in DocumentState for token generation
state.failedRanges = partialAst.failedRanges;
```

### Semantic Token Handling
The `ASTSemanticVisitor` receives the merged AST. It should work normally since:
- All locations are rebased to global positions
- Nodes are in document order
- Failed ranges just mean fewer nodes (gaps in tokens)

### Diagnostic Pipeline
Diagnostics from chunk failures should appear as normal errors:
- Position already rebased
- Message indicates syntax error
- No special handling needed

### Backwards Compatibility
- Keep `attemptPartialParsing` as fallback
- Environment variable to disable: `MLLD_CHUNK_PARSING=0`
- Same return shape as current implementation

### Deliverable
- [ ] Implement `parseWithChunks` as wrapper
- [ ] Wire into `analyzeDocument` at line 566
- [ ] Add `MLLD_CHUNK_PARSING` env var check
- [ ] Keep legacy fallback for safety
- [ ] Verify semantic tokens still work
- [ ] Verify diagnostics appear correctly


