---
id: mlld-gj7
status: closed
deps: []
links: []
created: 2025-12-08T19:57:33.602447-08:00
type: task
priority: 1
parent: mlld-k4k
---
# Grammar: add mode flag and optional slash prefix

**Summary:**
Add a `mode: 'strict' | 'markdown'` option to the parser and make the leading `/` optional on directives.

**Changes required:**

1. **Parser options** (`grammar/parser/index.ts` or entry point):
   - Accept `mode` option, default to `'markdown'` for backward compat during transition
   - Thread mode into grammar context

2. **Grammar helper** (`grammar/deps/grammar-core.ts`):
   - Replace `isSlashDirectiveContext` with `isDirectiveContext`
   - In both modes, match optional `/` followed by directive keyword at line start
   - The difference is handled at top-level line classification, not here

3. **Directive rules** (`grammar/mlld.peggy` or modular files):
   - Change directive prefix from `"/"` to `"/"?`
   - All directives: var, exe, run, show, for, when, import, export, guard, output, log, append, stream, needs, wants
   - Keep semantic actions unchanged

4. **Top-level line rule**:
   ```peggy
   Line 
     = Directive
     / BlankLine
     / &{ return options.mode === 'markdown' } TextLine
     / &{ return options.mode === 'strict' } StrictModeTextError
   
   StrictModeTextError = (!Directive .)+ { 
     error("Text content not allowed in strict mode (.mld). Use .mld.md for prose.") 
   }
   ```

5. **Blank line handling**:
   - Strict mode: blank lines are whitespace, produce no AST node
   - Markdown mode: blank lines may become content nodes (current behavior)

**Testing:**
- Add grammar unit tests for optional slash parsing
- Verify existing tests pass with mode='markdown'


