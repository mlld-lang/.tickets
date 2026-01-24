---
id: mlld-nlu.7
status: closed
deps: [mlld-nlu.6]
links: []
created: 2025-12-11T22:11:46.583203-08:00
type: task
priority: 2
parent: mlld-nlu
---
# mlld-nlu P6: Debug and telemetry

## Debug and Telemetry

### Existing Debug Pattern
The LSP uses `DEBUG=mlld:lsp` environment variable:
```typescript
import { logger } from '@core/utils/logger';

// Usage throughout language-server-impl.ts:
logger.debug('message', { data });
logger.warn('message');
```

### Chunk-Specific Logging
Add logging for chunk operations:
```typescript
// In splitIntoChunks:
logger.debug('Splitting document into chunks', { 
  totalLines: lines.length,
  mode 
});

// After splitting:
logger.debug('Created chunks', { 
  count: chunks.length,
  sizes: chunks.map(c => c.endLine - c.startLine + 1)
});

// In parseChunk:
logger.debug('Parsing chunk', { 
  startLine: chunk.startLine,
  endLine: chunk.endLine,
  length: chunk.text.length
});

// On chunk failure:
logger.debug('Chunk parse failed', {
  startLine: chunk.startLine,
  error: error.message
});

// In mergeChunkResults:
logger.debug('Merged chunk results', {
  totalNodes: nodes.length,
  failedChunks: results.filter(r => !r.success).length,
  successChunks: results.filter(r => r.success).length
});
```

### Performance Telemetry
Track timing for optimization:
```typescript
const splitStart = Date.now();
const chunks = splitIntoChunks(text, mode);
const splitTime = Date.now() - splitStart;

const parseStart = Date.now();
const results = await Promise.all(chunks.map(c => parseChunk(c, mode)));
const parseTime = Date.now() - parseStart;

logger.debug('Chunk parsing performance', {
  splitTime,
  parseTime,
  totalTime: splitTime + parseTime,
  chunkCount: chunks.length
});
```

### Debug Output Format
When `DEBUG=mlld:lsp`, output should show:
```
[mlld:lsp] Splitting document into chunks { totalLines: 50, mode: 'strict' }
[mlld:lsp] Created chunks { count: 5, sizes: [10, 8, 12, 15, 5] }
[mlld:lsp] Parsing chunk { startLine: 0, endLine: 9 }
[mlld:lsp] Parsing chunk { startLine: 10, endLine: 17 }
[mlld:lsp] Chunk parse failed { startLine: 18, error: 'Unexpected token' }
[mlld:lsp] Parsing chunk { startLine: 30, endLine: 44 }
[mlld:lsp] Parsing chunk { startLine: 45, endLine: 49 }
[mlld:lsp] Merged chunk results { totalNodes: 12, failedChunks: 1, successChunks: 4 }
[mlld:lsp] Chunk parsing performance { splitTime: 2, parseTime: 45, totalTime: 47 }
```

### Deliverable
- [ ] Add chunk splitting logs
- [ ] Add per-chunk parse logs
- [ ] Add merge summary log
- [ ] Add performance timing
- [ ] Follow existing logger patterns


