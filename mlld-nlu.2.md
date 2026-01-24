---
id: mlld-nlu.2
status: closed
deps: [mlld-nlu.1]
links: []
created: 2025-12-11T22:10:28.642294-08:00
type: task
priority: 2
parent: mlld-nlu
---
# mlld-nlu P1: Data structures and contracts

## Data Structures and Contracts

### Chunk Data Structure
```typescript
interface Chunk {
  text: string;           // The chunk content
  startLine: number;      // 0-based line number in original document
  endLine: number;        // 0-based end line (inclusive)
  startOffset: number;    // Character offset from document start
  endOffset: number;      // Character offset of chunk end
  startColumn: number;    // Column of first char (usually 0)
}
```

### Parse Result Structure
```typescript
interface ChunkParseResult {
  success: boolean;
  chunk: Chunk;
  ast: MlldNode[];        // Empty if failed
  diagnostics: Diagnostic[];
  error?: Error;          // Original parse error if failed
}
```

### Merged Result Structure
```typescript
interface MergedParseResult {
  nodes: MlldNode[];      // All successfully parsed nodes
  errors: Diagnostic[];   // All diagnostics including chunk failures
  failedRanges: Range[];  // Ranges that couldn't be parsed (for token gaps)
}
```

### Location Rebase Requirements

**Current AST location shape:**
```typescript
node.location = {
  start: { line: number, column: number, offset: number },
  end: { line: number, column: number, offset: number }
}
```

**All fields must be rebased:**
```typescript
function rebaseLocation(loc: Location, chunk: Chunk): Location {
  return {
    start: {
      line: loc.start.line + chunk.startLine,
      column: loc.start.line === 1 ? loc.start.column + chunk.startColumn : loc.start.column,
      offset: loc.start.offset + chunk.startOffset
    },
    end: {
      line: loc.end.line + chunk.startLine,
      column: loc.end.line === 1 ? loc.end.column + chunk.startColumn : loc.end.column,
      offset: loc.end.offset + chunk.startOffset
    }
  };
}
```

### Invariants (document in code comments)
1. Chunks are ordered by startOffset, no overlaps
2. Rebased locations are globally valid (can index into original text)
3. AST nodes are cloned before mutation (no shared references)
4. Failed chunks produce diagnostics covering their full range

### Deliverable
- [ ] Define types in `language-server-impl.ts` or new file
- [ ] Document invariants in JSDoc comments
- [ ] Ensure rebase handles first-line column edge case


