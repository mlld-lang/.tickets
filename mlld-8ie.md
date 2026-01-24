---
id: mlld-8ie
status: closed
deps: [mlld-p25]
links: []
created: 2025-12-11T20:12:18.008524-08:00
type: task
priority: 1
parent: mlld-a03
---
# LSP: Update for rc78 grammar changes

## Summary
Update language server semantic tokens for all rc78 grammar changes.

**Depends on:** mlld-p25 (mode detection) must be completed first.

## Syntax Changes to Support

### Block Syntax `[...]` (HIGH PRIORITY)
Exe, for, when blocks using bracket delimiters:
```mlld
/exe @func() = [
  let @x = 1
  let @y = 2
  => @x + @y
]

/for @item in @items [
  show @item
  let @count += 1
]
```

**Files:** DirectiveVisitor.ts, ExpressionVisitor.ts
**Tokens needed:**
- `[` and `]` as operators
- Block body contents (delegate to existing visitors)

### let Keyword (MEDIUM)
Block-scoped variable declarations:
```mlld
let @x = value
let @count += 1
```

**Files:** DirectiveVisitor.ts
**Tokens:** `let` as keyword, variable as declaration

### while Loops (MEDIUM)
```mlld
/while (100) @processor
done @value
continue @newState
```

**Files:** DirectiveVisitor.ts (new handler)
**Tokens:** `while` as keyword, `done`/`continue` as keywords, limit number

### stream Keyword (LOW)
```mlld
/stream @output
stream @pipe
```

**Files:** DirectiveVisitor.ts
**Tokens:** `stream` as keyword

### Command Working Directories (LOW)
```mlld
/run cmd:/abs/path { command }
/run sh:/abs/path { script }
```

**Files:** CommandVisitor.ts
**Tokens:** language label includes path, `:` and path as string

### When Semicolons (LOW)
```mlld
/when @x > 0 => "positive"; @x < 0 => "negative"; true => "zero"
```

**Files:** DirectiveVisitor.ts, ExpressionVisitor.ts
**Tokens:** `;` as operator between arms

### Comments in Blocks (LOW)
```mlld
/exe @func() = [
  >> This is a comment
  let @x = 1  << inline comment
]
```

**Files:** DirectiveVisitor.ts (already has CommentHelper)
**Tokens:** Use existing comment tokenization

### AST Selector Wildcards (MEDIUM)
```mlld
<file.ts { handle* }>
<file.ts { *fn }>
<file.ts { ?? }>
```

**Files:** FileReferenceVisitor.ts or new
**Tokens:** `*`, `?`, `??` patterns

### Variable Boundary Escaping (LOW)
```mlld
@var\.ext
```

**Files:** VariableVisitor.ts
**Tokens:** Handle backslash-dot as part of variable

## Implementation Approach

1. Start with block syntax `[...]` as it's most visible
2. Add `let` keyword support
3. Add `while` loop support
4. Handle remaining items

## Testing
```bash
npm test services/lsp/semantic-tokens.test.ts
npm test services/lsp/semantic-tokens-unit.test.ts
```

## Acceptance Criteria
- [ ] Block syntax `[...]` highlights correctly
- [ ] `let` keyword highlighted as keyword
- [ ] `while`/`done`/`continue` keywords highlighted
- [ ] `stream` keyword highlighted
- [ ] Working directory paths highlighted as strings
- [ ] Semicolon separators highlighted as operators
- [ ] Comments in blocks work
- [ ] AST selector wildcards highlighted
- [ ] Variable boundary escaping works


