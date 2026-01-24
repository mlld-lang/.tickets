---
id: mlld-a03
status: closed
deps: []
links: []
created: 2025-12-11T20:12:45.617648-08:00
type: epic
priority: 0
---
# LSP: Comprehensive update for rc78 and editor support

Epic tracking comprehensive LSP updates for:
1. rc78 grammar changes (blocks, let, while, streaming, etc.)
2. Strict mode support
3. Outstanding editor-support GitHub issues

## Architecture Overview

The LSP implementation uses a visitor-based architecture:

**Core Files:**
- `cli/commands/language-server-impl.ts` - Main LSP server, handles parsing and semantic tokens
- `services/lsp/ASTSemanticVisitor.ts` - Dispatches nodes to specialized visitors
- `services/lsp/visitors/` - Specialized visitors for different node types:
  - `DirectiveVisitor.ts` - Handles /var, /show, /when, /for, /exe, etc.
  - `ExpressionVisitor.ts` - Binary, unary, ternary, when, for expressions
  - `VariableVisitor.ts` - Variable references and field access
  - `CommandVisitor.ts` - Command execution and references
  - `TemplateVisitor.ts` - String literals and templates
  - `StructureVisitor.ts` - Objects, arrays, properties
  - `FileReferenceVisitor.ts` - File references, comments, frontmatter
  - `ForeachVisitor.ts` - Foreach command syntax

**Helper Classes in `services/lsp/utils/`:**
- `OperatorTokenHelper.ts` - Operator tokenization
- `CommentTokenHelper.ts` - Comment handling
- `LanguageBlockHelper.ts` - Embedded language blocks
- `TemplateTokenHelper.ts` - Template contexts
- `TokenBuilder.ts` - Token generation

**Current Gap:** The LSP always parses with `mode: 'markdown'` (hardcoded default). It doesn't detect file extensions or pass mode to the parser.

## Execution Strategy

### Phase 1: Mode-Aware Foundation (CRITICAL PATH)
Unblocks all other work. Must complete first.
- Detect `.mld` vs `.mld.md` from document URI
- Pass mode to parser in all parse() calls
- Adjust DirectiveVisitor for bare directives in strict mode

### Phase 2: rc78 Grammar Changes (HIGH VALUE)
Independent features, can be parallelized:
- Block syntax `[...]` for exe/for/when
- `let` keyword for block-scoped variables
- `while` loops with `done`/`continue`
- `stream` keyword and `/stream` directive
- Working directories `cmd:/path`
- When semicolons
- Comments in blocks
- AST selector wildcards
- Variable boundary escaping

### Phase 3: Bug Fixes (QUICK WINS)
Small, scoped fixes from GitHub issues:
- GH#327: when block assignment
- GH#328: pipe transform parity
- GH#329: EOL comments in when
- GH#330: variable interpolation in /run
- GH#331: function execution in /run
- GH#332: array/object value highlighting

### Phase 4: Error Recovery (LARGER SCOPE)
- Chunk-based parsing for better error recovery (GH#335)

## Testing

```bash
# Run LSP tests
npm test services/lsp/

# Run specific test file
npm test services/lsp/semantic-tokens.test.ts

# Run coverage tests
MLLD_TOKEN_COVERAGE=1 npm test services/lsp/semantic-tokens-coverage.test.ts
```

## Debugging

```bash
# Server-side debug logging
DEBUG=mlld:lsp mlld language-server

# AST inspection
npm run ast -- 'your syntax here'
```

See `docs/dev/LANGUAGE-SERVER.md` for full documentation.


