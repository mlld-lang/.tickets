---
id: mlld-nlu.4
status: closed
deps: [mlld-nlu.3]
links: []
created: 2025-12-11T22:11:00.099048-08:00
type: task
priority: 2
parent: mlld-nlu
---
# mlld-nlu P3: Per-chunk parsing

## Per-Chunk Parsing

### Core Function
```typescript
async function parseChunk(chunk: Chunk, mode: MlldMode): Promise<ChunkParseResult>
```

### Implementation
```typescript
async function parseChunk(chunk: Chunk, mode: MlldMode): Promise<ChunkParseResult> {
  try {
    const result = await parse(chunk.text, { mode });
    
    if (!result.success) {
      return {
        success: false,
        chunk,
        ast: [],
        diagnostics: [createChunkDiagnostic(chunk, result.error)],
        error: result.error
      };
    }
    
    // Clone and rebase AST nodes
    const rebasedAst = rebaseAst(result.ast, chunk);
    
    // Rebase any warnings/diagnostics from parser
    const rebasedDiagnostics = result.warnings.map(w => 
      rebaseDiagnostic(w, chunk)
    );
    
    return {
      success: true,
      chunk,
      ast: rebasedAst,
      diagnostics: rebasedDiagnostics
    };
  } catch (error) {
    return {
      success: false,
      chunk,
      ast: [],
      diagnostics: [createChunkDiagnostic(chunk, error)],
      error
    };
  }
}
```

### AST Rebasing (Critical)
```typescript
function rebaseAst(ast: MlldNode[], chunk: Chunk): MlldNode[] {
  // Deep clone to avoid mutating shared references
  const cloned = JSON.parse(JSON.stringify(ast));
  
  // Recursively rebase all locations
  function rebaseNode(node: any): void {
    if (!node || typeof node !== 'object') return;
    
    if (node.location) {
      // Rebase ALL location fields
      if (node.location.start) {
        node.location.start.line += chunk.startLine;
        node.location.start.offset += chunk.startOffset;
        // Column only needs adjustment for first line of chunk
        if (node.location.start.line === chunk.startLine && chunk.startColumn > 0) {
          node.location.start.column += chunk.startColumn;
        }
      }
      if (node.location.end) {
        node.location.end.line += chunk.startLine;
        node.location.end.offset += chunk.startOffset;
        if (node.location.end.line === chunk.startLine && chunk.startColumn > 0) {
          node.location.end.column += chunk.startColumn;
        }
      }
    }
    
    // Recurse into all properties
    for (const key in node) {
      const value = node[key];
      if (Array.isArray(value)) {
        value.forEach(rebaseNode);
      } else if (value && typeof value === 'object') {
        rebaseNode(value);
      }
    }
  }
  
  cloned.forEach(rebaseNode);
  return cloned;
}
```

### Why Clone is Necessary
The parser may return cached AST nodes or share references between nodes. Without cloning:
- Rebasing one chunk could corrupt another
- Cached parse results would have wrong locations on reuse

### Chunk Diagnostic Creation
```typescript
function createChunkDiagnostic(chunk: Chunk, error: any): Diagnostic {
  // Try to get specific location from error
  const errorLine = error?.location?.start?.line ?? 1;
  const errorCol = error?.location?.start?.column ?? 0;
  
  return {
    severity: DiagnosticSeverity.Error,
    range: {
      start: { 
        line: chunk.startLine + errorLine - 1, 
        character: errorCol 
      },
      end: { 
        line: chunk.startLine + errorLine - 1, 
        character: errorCol + 1 
      }
    },
    message: error?.message || 'Syntax error',
    source: 'mlld'
  };
}
```

### Short-Circuit Optimization
```typescript
// If document is a single chunk and fails, return immediately
// (equivalent to current full-parse failure behavior)
if (chunks.length === 1 && !parseResult.success) {
  return { nodes: [], errors: [originalDiagnostic] };
}
```

### Deliverable
- [ ] Implement `parseChunk` with proper error handling
- [ ] Implement `rebaseAst` with deep clone and full location rebase
- [ ] Verify offsets are correct by testing with TokenBuilder
- [ ] Add short-circuit for single-chunk failures


