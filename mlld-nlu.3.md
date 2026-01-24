---
id: mlld-nlu.3
status: closed
deps: [mlld-nlu.2]
links: []
created: 2025-12-11T22:10:44.610126-08:00
type: task
priority: 2
parent: mlld-nlu
---
# mlld-nlu P2: Chunk splitter

## Chunk Splitter Implementation

### Core Function
```typescript
function splitIntoChunks(text: string, mode: MlldMode): Chunk[]
```

### Depth Tracking State
```typescript
interface SplitterState {
  bracketDepth: number;   // [...]  - rc78 blocks
  braceDepth: number;     // {...}  - code/data
  parenDepth: number;     // (...)  - conditions
  templateStack: ('`' | '::' | ':::')[];  // Nested templates
  inComment: boolean;     // >> ... (to newline)
  inCodeFence: boolean;   // ``` ... ```
}
```

### Constructs That Prevent Splitting
1. **Block syntax `[...]`** - rc78 exe/for/when blocks can span many lines
2. **Code blocks `{...}`** - Commands, data objects
3. **Templates** - Backticks, `::`, `:::` can be multi-line
4. **Code fences** - ``` blocks in markdown mode
5. **Comments** - `>>` to end of line

### Split Point Detection
```typescript
function isSplitPoint(line: string, prevLine: string, state: SplitterState, mode: MlldMode): boolean {
  // Never split when inside a construct
  if (state.bracketDepth > 0 || state.braceDepth > 0 || 
      state.parenDepth > 0 || state.templateStack.length > 0 ||
      state.inCodeFence) {
    return false;
  }
  
  // Split on blank line followed by directive
  if (prevLine.trim() === '' && isDirectiveStart(line, mode)) {
    return true;
  }
  
  // Split at document start directive
  return false;
}

function isDirectiveStart(line: string, mode: MlldMode): boolean {
  const trimmed = line.trim();
  
  // Markdown mode: must start with /
  if (mode === 'markdown') {
    return trimmed.startsWith('/');
  }
  
  // Strict mode: bare directives are also valid
  const bareDirectives = /^(var|show|exe|run|for|when|while|stream|guard|import|export|output|append|log|path)\b/;
  return trimmed.startsWith('/') || bareDirectives.test(trimmed);
}
```

### Scanner Algorithm
```typescript
function splitIntoChunks(text: string, mode: MlldMode): Chunk[] {
  const chunks: Chunk[] = [];
  const lines = text.split('\n');
  const state: SplitterState = { /* init zeros */ };
  
  let chunkStart = 0;
  let chunkStartOffset = 0;
  let currentOffset = 0;
  
  for (let i = 0; i < lines.length; i++) {
    const line = lines[i];
    const prevLine = i > 0 ? lines[i-1] : '';
    
    // Update state by scanning line
    updateState(state, line);
    
    // Check for split point
    if (i > 0 && isSplitPoint(line, prevLine, state, mode)) {
      // Emit previous chunk
      chunks.push({
        text: lines.slice(chunkStart, i).join('\n'),
        startLine: chunkStart,
        endLine: i - 1,
        startOffset: chunkStartOffset,
        endOffset: currentOffset - 1, // exclude newline
        startColumn: 0
      });
      chunkStart = i;
      chunkStartOffset = currentOffset;
    }
    
    currentOffset += line.length + 1; // +1 for newline
  }
  
  // Emit final chunk
  // ...
  
  return chunks;
}
```

### Safety Limits
- Cap chunk count at 200 - if exceeded, return single chunk (full document)
- Minimum chunk size: 1 line
- Log warning when falling back to single chunk

### Edge Cases
- Empty document → single empty chunk
- Document with only errors → single chunk
- Unclosed bracket at EOF → include in last chunk
- Template spanning entire document → single chunk

### Deliverable
- [ ] Implement `splitIntoChunks` with depth tracking
- [ ] Handle all template types (`, ::, :::)
- [ ] Support both strict and markdown modes
- [ ] Add chunk count safety limit
- [ ] Unit tests for split point detection


