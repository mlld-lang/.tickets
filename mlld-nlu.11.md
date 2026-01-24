---
id: mlld-nlu.11
status: closed
deps: [mlld-nlu.10]
links: []
created: 2025-12-11T22:12:48.175647-08:00
type: task
priority: 2
parent: mlld-nlu
---
# mlld-nlu P10: Fallback and toggle

## Fallback and Toggle

### Environment Variable Toggle
Follow existing patterns in the codebase:
```typescript
// Existing patterns:
// - MLLD_STRICT=1/0 for mode override
// - DEBUG=mlld:lsp for logging
// - MLLD_NO_STREAM for streaming

// New toggle:
const CHUNK_PARSING_ENABLED = process.env.MLLD_CHUNK_PARSING !== '0';
```

### Implementation
```typescript
async function parseWithChunks(
  text: string,
  originalError: any,
  mode: MlldMode
): Promise<MergedParseResult> {
  
  // Toggle: disable chunk parsing via env var
  if (!CHUNK_PARSING_ENABLED) {
    logger.debug('Chunk parsing disabled via MLLD_CHUNK_PARSING=0');
    const result = await attemptPartialParsing(text, originalError, mode);
    return { nodes: result.nodes, errors: result.errors, failedRanges: [] };
  }
  
  // ... chunk parsing logic ...
  
  // Fallback: if all chunks fail, try legacy approach
  if (merged.nodes.length === 0 && results.every(r => !r.success)) {
    logger.debug('All chunks failed, falling back to legacy parsing');
    const legacy = await attemptPartialParsing(text, originalError, mode);
    return { nodes: legacy.nodes, errors: legacy.errors, failedRanges: [] };
  }
  
  return merged;
}
```

### Fallback Scenarios
1. **Env var disabled** (`MLLD_CHUNK_PARSING=0`): Use legacy `attemptPartialParsing`
2. **Too many chunks** (>200): Fall back to single chunk
3. **All chunks fail**: Try legacy approach as last resort
4. **Splitter error**: Catch and fall back to legacy

### Error Handling
```typescript
try {
  const chunks = splitIntoChunks(text, mode);
  // ...
} catch (e) {
  logger.warn('Chunk splitting failed, using legacy parsing', { error: e.message });
  const legacy = await attemptPartialParsing(text, originalError, mode);
  return { nodes: legacy.nodes, errors: legacy.errors, failedRanges: [] };
}
```

### Documentation for Users
Add to `docs/dev/LANGUAGE-SERVER.md`:
```markdown
### Chunk-Based Error Recovery

The language server uses chunk-based parsing to recover from syntax errors.
When a document has errors, it splits the document into chunks and parses
each independently, preserving highlighting for valid sections.

To disable (for debugging):
```bash
MLLD_CHUNK_PARSING=0 mlld language-server
```
```

### Deliverable
- [ ] Add `MLLD_CHUNK_PARSING` env var check
- [ ] Implement all fallback scenarios
- [ ] Add error handling around splitter
- [ ] Document env var in LANGUAGE-SERVER.md
- [ ] Test fallback behavior


