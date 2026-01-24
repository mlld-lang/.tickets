---
id: mlld-tfb
status: closed
deps: [mlld-5n7]
links: []
created: 2025-12-08T19:58:17.200221-08:00
type: task
priority: 2
parent: mlld-k4k
---
# Tests: strict mode fixtures and regressions

**Summary:**
Add comprehensive test coverage for strict mode parsing and execution.

**New fixtures needed:**

1. **Strict mode success cases** (`tests/cases/valid/strict/`):
   - `bare-directives.mld` - directives without `/` prefix
   - `optional-slash.mld` - directives WITH `/` prefix (backward compat)
   - `mixed-slash.mld` - some with, some without `/`
   - `blank-lines.mld` - blank lines as formatting (should be ignored)
   - `all-directives.mld` - every directive type without slash

2. **Strict mode error cases** (`tests/cases/invalid/strict/`):
   - `text-line.mld` - plain text should error
   - `prose-content.mld` - markdown prose should error
   - `comment-looking-text.mld` - `# heading` (not a directive) should error

3. **Markdown mode regressions** (`tests/cases/valid/markdown/`):
   - Existing tests renamed to `.mld.md` to confirm they still work
   - Or keep as `.mld` with explicit mode override for backward compat testing

4. **Cross-mode imports** (`tests/cases/valid/imports/`):
   - `strict-imports-markdown.mld` imports `helper.mld.md`
   - `markdown-imports-strict.mld.md` imports `helper.mld`

**Fixture structure:**
```
tests/cases/valid/strict/
  bare-directives/
    example.mld      # bare var, exe, show, etc.
    expected.md      # output
tests/cases/invalid/strict/
  text-line/
    example.mld      # "This is text"
    error.md         # expected error pattern
```

**Testing approach:**
- Run `npm run build:fixtures` to generate fixture JSON
- Verify strict mode fixtures parse/execute correctly
- Verify invalid strict cases produce clear errors


