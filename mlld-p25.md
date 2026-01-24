---
id: mlld-p25
status: closed
deps: []
links: []
created: 2025-12-11T21:00:19.943624-08:00
type: task
priority: 0
parent: mlld-a03
---
# LSP: Add mode detection from file URI

## Summary
Add mode detection to the language server so it correctly parses .mld files in strict mode and .mld.md files in markdown mode.

**This is the critical foundation that unblocks all other LSP work.**

## Current State
- `cli/commands/language-server-impl.ts` calls `parse(text)` without passing mode
- `grammar/parser/index.ts` defaults to `mode: 'markdown'`
- DirectiveVisitor doesn't know about mode, always expects `/` prefix

## Implementation

### Step 1: Add getModeFromUri helper
Add to `language-server-impl.ts` around line 40:

```typescript
import type { MlldMode } from '@core/types';

function getModeFromUri(uri: string): MlldMode {
  // file:///path/to/file.mld -> strict
  // file:///path/to/file.mld.md -> markdown
  // file:///path/to/file.md -> markdown
  const path = uri.replace('file://', '');
  if (path.endsWith('.mld.md') || path.endsWith('.mlld.md')) {
    return 'markdown';
  }
  if (path.endsWith('.mld') || path.endsWith('.mlld')) {
    return 'strict';
  }
  // Default to markdown for .md and unknown
  return 'markdown';
}
```

### Step 2: Update analyzeDocument function
Around line 478, change:
```typescript
const result = await parse(text);
```
to:
```typescript
const mode = getModeFromUri(document.uri);
const result = await parse(text, { mode });
```

### Step 3: Update attemptPartialParsing
Around line 1378 and 1435, ensure mode is passed to parse() calls.

### Step 4: Store mode in document state
Update the DocumentState interface and getOrCreateDocumentState to track mode.

### Step 5: Pass mode to ASTSemanticVisitor
The visitor needs to know the mode to correctly tokenize bare directives in strict mode.

## Files to Modify
- `cli/commands/language-server-impl.ts` - Main changes
- `services/lsp/ASTSemanticVisitor.ts` - Accept mode parameter
- `services/lsp/visitors/DirectiveVisitor.ts` - Handle bare directives

## Testing
```bash
# Create test files
echo 'var @x = "test"' > tmp/test-strict.mld
echo '/var @x = "test"' > tmp/test-markdown.mld.md

# Run LSP tests
npm test services/lsp/semantic-tokens.test.ts
```

## Acceptance Criteria
- [ ] .mld files parse in strict mode
- [ ] .mld.md files parse in markdown mode
- [ ] Mode is available in DirectiveVisitor
- [ ] Bare directives in strict mode get keyword highlighting


