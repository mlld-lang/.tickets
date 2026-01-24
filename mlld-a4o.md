---
id: mlld-a4o
status: closed
deps: []
links: []
created: 2025-12-10T10:45:20.704254-08:00
type: feature
priority: 2
---
# Semicolon separator for single-line blocks

Support semicolon as statement separator in exe/for blocks when written on single line.

**Current**: Whitespace-separated works but hard to read
```mlld
/exe @func() = [let @x = 1  let @y = 2  => @x + @y]
```

**Target**: Semicolon separator for clarity
```mlld
/exe @func() = [let @x = 1; let @y = 2; => @x + @y]
/for @item in @items [show @item; let @count += 1]
```

**Grammar changes needed**:
- Update `BlockStatementSeparator` in `grammar/patterns/iteration.peggy` to accept `;` in addition to whitespace
- Update `ExeBlockBody` in `grammar/patterns/exe-rhs.peggy` similarly
- Ensure semicolon is optional (whitespace/newline still works)
- AST unchanged - semicolon is purely syntactic sugar for readability

**Test cases**:
- Single-line with semicolons parses correctly
- Multi-line without semicolons still works
- Mixed: some semicolons, some newlines works
- Error: comma rejected with helpful message (already implemented)

**CHANGELOG**: Already documented in rc78 - this makes it real.


