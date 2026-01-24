---
id: mlld-om1
status: closed
deps: [mlld-tfb, mlld-ah5, mlld-cgb]
links: []
created: 2025-12-08T19:58:27.625577-08:00
type: task
priority: 3
parent: mlld-k4k
---
# LSP: mode-aware syntax highlighting and errors

## Summary
Comprehensive mode-aware LSP behavior including syntax highlighting, diagnostics, and completion.

**Depends on:** mlld-p25 (mode detection), mlld-cgb (highlighting basics)

**Overlap note:** mlld-cgb covers basic highlighting. This task covers the broader UX including error messages, completion, and formatting hints.

## Scope

### 1. File Extension Detection ✓ (covered by mlld-p25)
- `.mld` → strict mode
- `.mld.md` → markdown mode

### 2. Syntax Highlighting ✓ (covered by mlld-cgb)
- Strict: bare directives as keywords
- Markdown: only `/keyword` as keywords

### 3. Error Diagnostics (THIS TASK)
When text lines appear in strict mode:
```mlld
var @x = "test"
This is prose content  << Should show error squiggle
```

**Implementation:**
In `language-server-impl.ts`, when mode is strict and we encounter text nodes:

```typescript
// During validation, if mode is strict
if (mode === 'strict') {
  for (const node of ast) {
    if (node.type === 'Text' && node.content?.trim()) {
      diagnostics.push({
        severity: DiagnosticSeverity.Error,
        range: getNodeRange(node),
        message: 'Text content not allowed in strict mode (.mld files). Use /show for output or rename to .mld.md for prose.',
        source: 'mlld'
      });
    }
  }
}
```

### 4. Hover/Completion (THIS TASK)
Directive completions should adapt to mode:

**Strict mode:**
- Suggest both `var` and `/var`
- Prefer bare form in suggestions

**Markdown mode:**
- Only suggest `/var` form

**Implementation:**
In completion provider around line 750+:

```typescript
const mode = getModeFromUri(document.uri);
const prefix = mode === 'strict' ? '' : '/';

const directives = ['var', 'show', 'exe', 'run', 'for', 'when', ...];
return directives.map(d => ({
  label: `${prefix}${d}`,
  kind: CompletionItemKind.Keyword,
  // ...
}));
```

### 5. Formatter Hints (OPTIONAL/FUTURE)
Could suggest normalizing to bare directives in strict mode:
```
Hint: In strict mode, '/var' can be simplified to 'var'
```

## Files to Modify
- `cli/commands/language-server-impl.ts` - Diagnostics and completion
- Consider: `services/lsp/` if visitor changes needed

## Testing
```bash
npm test cli/commands/language-server.test.ts
```

## Acceptance Criteria
- [ ] Text in strict mode shows error diagnostic
- [ ] Error message suggests renaming to .mld.md
- [ ] Completions adapt to mode
- [ ] Bare directive completions in strict mode

## Size
Medium


