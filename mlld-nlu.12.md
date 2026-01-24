---
id: mlld-nlu.12
status: closed
deps: [mlld-nlu.11]
links: []
created: 2025-12-11T22:13:03.650862-08:00
type: task
priority: 2
parent: mlld-nlu
---
# mlld-nlu P11: Finalize

## Finalize

### Pre-Merge Checklist

**Code Quality:**
- [ ] All functions have JSDoc comments
- [ ] Type definitions are complete
- [ ] No `any` types without justification
- [ ] Error messages are user-friendly
- [ ] Debug logging follows existing patterns

**Testing:**
- [ ] All unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing with real documents
- [ ] Tested with strict and markdown modes
- [ ] Tested env var toggle

**Performance:**
- [ ] Chunk splitting is O(n) in document size
- [ ] No memory leaks from cloning
- [ ] Acceptable latency for typical documents
- [ ] Chunk count cap prevents runaway splitting

**Backwards Compatibility:**
- [ ] Legacy `attemptPartialParsing` still available
- [ ] Same return shape for callers
- [ ] No breaking changes to LSP protocol
- [ ] Env var allows disabling new behavior

### Final Verification

```bash
# Build
npm run build

# Run all LSP tests
npm test services/lsp/

# Manual test with debug logging
DEBUG=mlld:lsp mlld language-server

# Test in editor
# 1. Open .mld file with syntax error
# 2. Verify highlighting works before/after error
# 3. Verify error squiggle appears on error line
```

### PR Description Template

```markdown
## Summary
Implements chunk-based parsing for LSP error recovery (closes #335).

## Changes
- Add `splitIntoChunks` with depth-aware boundary detection
- Add `parseChunk` with location rebasing
- Add `mergeChunkResults` for AST/diagnostic combination
- Replace `attemptPartialParsing` call with `parseWithChunks`
- Add `MLLD_CHUNK_PARSING=0` toggle for fallback

## Testing
- Unit tests for chunk splitting
- Unit tests for location rebasing
- Integration tests for error recovery scenarios
- Manual testing in VSCode

## Notes
- Falls back to legacy parsing if all chunks fail
- Respects block syntax, templates, and code fences
- Caps chunk count at 200 for safety
```

### Deliverable
- [ ] Complete pre-merge checklist
- [ ] Run final verification
- [ ] Create PR with description
- [ ] Address review feedback


