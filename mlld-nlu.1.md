---
id: mlld-nlu.1
status: closed
deps: []
links: []
created: 2025-12-11T22:10:12.629701-08:00
type: task
priority: 2
parent: mlld-nlu
---
# mlld-nlu P0: Grounding

## Grounding - Understand Current Implementation

### Files to Read
1. `cli/commands/language-server-impl.ts` - Main LSP implementation

### Key Functions (with line numbers)
- **`attemptPartialParsing`** (line 1438): Entry point for error recovery
  ```typescript
  async function attemptPartialParsing(
    text: string,
    originalError: any,
    mode: MlldMode  // Added recently - strict vs markdown
  ): Promise<{ nodes: any[], errors: Diagnostic[] }>
  ```

- **`tryParseBlock`** (line 1502): Parses individual blocks
  ```typescript
  async function tryParseBlock(
    block: string,
    startLine: number,
    nodes: any[],
    errors: Diagnostic[],
    mode: MlldMode
  ): Promise<void>
  ```

- **`adjustLineNumbers`** (line 1530): Rebases AST locations
  - Currently only adjusts `.line` - **NOT `.offset` or `.column`**
  - Mutates nodes directly (potential shared reference issue)

### Call Chain
```
analyzeDocument (line 478)
  → parse() fails
  → attemptPartialParsing (line 566)
    → tryParseBlock (line 1483, 1496)
      → parse()
      → adjustLineNumbers()
  → returned nodes fed to ASTSemanticVisitor
```

### Current Top-Level Detection (line 1476)
```typescript
const isTopLevel = trimmedLine.startsWith('/') || 
                  trimmedLine.startsWith('>>') ||
                  trimmedLine.startsWith('---') ||
                  trimmedLine.startsWith('```');
```
**Missing:** Bare directives in strict mode, bracket depth tracking

### Debug Logging
- `DEBUG=mlld:lsp` enables server-side logging
- Look for `logger.debug()` patterns to follow

### Semantic Token Dependencies
Visitors use `node.location.start.offset` extensively:
- `DirectiveVisitor.ts` line ~40: `node.location.start.offset`
- `OperatorTokenHelper.ts`: offset-based operator search
- `TokenBuilder.addToken()`: requires accurate positions

### Deliverable
Document findings in a comment or notes. Confirm:
- [ ] Understood current splitting logic limitations
- [ ] Understood location rebase requirements (line + offset + column)
- [ ] Understood mode parameter flow
- [ ] Identified debug logging patterns


