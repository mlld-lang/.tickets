---
id: mlld-nlu.9
status: closed
deps: [mlld-nlu.8]
links: []
created: 2025-12-11T22:12:17.943335-08:00
type: task
priority: 2
parent: mlld-nlu
---
# mlld-nlu P8: Docs

## Documentation

### Update docs/dev/LANGUAGE-SERVER.md

Add section after "Graceful Incomplete Line Handling" (~line 340):

```markdown
### Chunk-Based Error Recovery

When the parser encounters a syntax error, the language server uses chunk-based
parsing to recover as much valid AST as possible:

1. **Document Splitting**: The document is split into logical chunks at directive
   boundaries, respecting nested constructs (blocks, templates, code fences)

2. **Independent Parsing**: Each chunk is parsed independently with the appropriate
   mode (strict or markdown)

3. **Location Rebasing**: AST node locations are adjusted to global document positions

4. **Result Merging**: Successfully parsed chunks are merged; failed chunks produce
   error diagnostics

**Benefits:**
- Syntax errors don't break highlighting for the entire document
- Valid code before and after errors remains highlighted
- Multiple errors can be shown simultaneously

**Configuration:**
- Disable with `MLLD_CHUNK_PARSING=0` environment variable
- Falls back to line-by-line parsing if chunk parsing fails completely

**Limitations:**
- Errors inside multi-line constructs (blocks, templates) affect the entire construct
- Performance overhead for documents with many chunk boundaries
```

### Update Troubleshooting Section

Add under "Semantic Highlighting Not Working":

```markdown
7. **Error Recovery Not Working**:
   - Chunk-based parsing requires valid chunk boundaries
   - Errors inside `[...]` blocks affect the entire block
   - Try `MLLD_CHUNK_PARSING=0` to use legacy line-by-line recovery
   - Check debug logs: `DEBUG=mlld:lsp mlld language-server`
```

### Update Architecture Section

Add to "Analysis Pipeline" (~line 315):

```markdown
### Analysis Pipeline

1. **Parse** - Use mlld grammar to parse documents
2. **Chunk Recovery** - If parse fails, split and parse chunks (NEW)
3. **Analyze** - Extract variables, imports, exports
4. **Generate Tokens** - Create semantic tokens using AST visitor pattern
5. **Cache** - Store analysis results and text extracts for performance
6. **Update** - Send diagnostics, completions, and semantic tokens
```

### Deliverable
- [ ] Add "Chunk-Based Error Recovery" section
- [ ] Update troubleshooting section
- [ ] Update architecture section
- [ ] Keep consistent with existing doc style


