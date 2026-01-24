---
id: mlld-nlu
status: closed
deps: []
links: []
created: 2025-12-11T20:12:36.462075-08:00
type: task
priority: 2
parent: mlld-a03
---
# [Epic] LSP: Chunk-based parsing for error recovery (GH#335)

## Summary
Implement chunk-based parsing for better error recovery in the language server.

GitHub: https://github.com/mlld-lang/mlld/issues/335

## Problem
When a document has a syntax error, the entire document may fail to parse, losing all semantic tokens.

## Current Implementation

Located in `cli/commands/language-server-impl.ts`:
- `attemptPartialParsing(text, originalError, mode)` at **line 1438**
- `tryParseBlock(block, startLine, nodes, errors, mode)` at **line 1502**
- `adjustLineNumbers(ast, offset)` at **line 1530**
- Called from `analyzeDocument` at **line 566**

### Current Behavior
1. Parse text before error line
2. Iterate lines, detect "top-level" constructs by checking for `/`, `>>`, `---`, code fences
3. Parse each block, adjust line numbers, accumulate nodes

### Current Limitations
- **No bracket depth tracking** - splits inside `[...]` blocks, `{...}` code, templates
- **Only adjusts line numbers** - doesn't rebase `offset` or `column` in locations
- **Mutates AST nodes directly** - shared reference bugs possible
- **Strict mode not fully handled** - bare directives (`var`, `show`) not detected as top-level

## Proposed Approach

### Chunk-Based Strategy
1. Split document into logical chunks with **depth tracking** for `[]`, `{}`, `()`, templates
2. Parse each chunk independently with mode
3. Rebase **all location fields** (line, column, offset)
4. Merge successful ASTs, mark failed chunks with error spans

### Key Implementation Details

**Chunk Boundary Detection:**
```typescript
// Must track depth for these constructs:
// - Block syntax: [...] (rc78 exe/for/when blocks)
// - Code blocks: {...} (commands, data)
// - Templates: `, ::, ::: (multi-line)
// - Parentheses: (...) (conditions, params)

// Valid split points (when all depths === 0):
// - Blank line followed by directive
// - Markdown mode: line starting with /
// - Strict mode: line starting with directive keyword
```

**Location Rebasing (critical for semantic tokens):**
```typescript
// Current adjustLineNumbers only does:
node.location.start.line += offset;
node.location.end.line += offset;

// Must also adjust (used by TokenBuilder/visitors):
node.location.start.offset += chunkStartOffset;
node.location.end.offset += chunkStartOffset;
node.location.start.column  // only if chunk doesn't start at column 0
```

**Integration Point:**
```typescript
// In analyzeDocument (~line 566):
const partialAst = await attemptPartialParsing(text, error, mode);
// Replace with:
const partialAst = await parseWithChunks(text, error, mode);
```

## Files to Modify
- `cli/commands/language-server-impl.ts` - Replace attemptPartialParsing

## Testing
Create documents with errors at different positions:
- Error in first line
- Error in middle of document
- Error inside a `[...]` block
- Error inside a template
- Multiple errors
- Strict mode with bare directives

## Environment Variables
- `MLLD_CHUNK_PARSING=0` - Disable chunk parsing (fallback to current behavior)
- `DEBUG=mlld:lsp` - Existing debug logging, add chunk-specific logs

## Acceptance Criteria
- [ ] Documents with single error have mostly valid highlighting
- [ ] Error recovery preserves tokens before and after error
- [ ] Block constructs `[...]` not split incorrectly
- [ ] Multi-line templates not split incorrectly
- [ ] Performance acceptable (cap chunk count)
- [ ] Offsets correctly rebased for semantic tokens

## Size
Medium - requires careful design of chunk splitting strategy

Notes:
Epic for chunk-based parsing recovery; child phases P0-P11 cover grounding through finalize.

## Notes

Epic for chunk-based parsing recovery; child phases P0-P11 cover grounding through finalize.


