---
id: mlld-nlu.5
status: closed
deps: [mlld-nlu.4]
links: []
created: 2025-12-11T22:11:15.143493-08:00
type: task
priority: 2
parent: mlld-nlu
---
# mlld-nlu P4: Merge results

## Merge Results

### Core Function
```typescript
function mergeChunkResults(results: ChunkParseResult[]): MergedParseResult
```

### Implementation
```typescript
function mergeChunkResults(results: ChunkParseResult[]): MergedParseResult {
  const nodes: MlldNode[] = [];
  const errors: Diagnostic[] = [];
  const failedRanges: Range[] = [];
  
  // Results are already in order (chunks were ordered)
  for (const result of results) {
    if (result.success) {
      nodes.push(...result.ast);
    } else {
      // Track failed range for token gap handling
      failedRanges.push({
        start: { line: result.chunk.startLine, character: 0 },
        end: { line: result.chunk.endLine, character: 0 }
      });
    }
    
    // Always include diagnostics (warnings from successful, errors from failed)
    errors.push(...result.diagnostics);
  }
  
  return { nodes, errors, failedRanges };
}
```

### Ordering Guarantees
- Chunks are split in document order
- Results processed in same order
- No sorting needed
- No deduplication needed (chunks don't overlap)

### Failed Range Handling
Failed ranges tell the token generator which parts of the document have no AST:
- Don't generate tokens for these ranges
- Show error squiggles instead
- Prevents "holes" in highlighting from causing confusion

### Edge Cases
- All chunks succeed → normal AST, no failed ranges
- All chunks fail → empty AST, full document is failed range
- Interleaved success/fail → partial AST with gaps

### Integration with Semantic Tokens
The `failedRanges` can be used by `ASTSemanticVisitor`:
```typescript
// In token generation, skip nodes that fall in failed ranges
// Or: generate error tokens for failed ranges
```

### Deliverable
- [ ] Implement `mergeChunkResults`
- [ ] Maintain ordering invariant
- [ ] Track failed ranges for downstream use
- [ ] No mutation of input results


