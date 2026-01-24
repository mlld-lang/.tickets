---
id: mlld-9i7
status: closed
deps: []
links: []
created: 2025-12-12T07:22:57.491298-08:00
type: task
priority: 0
parent: mlld-a03
---
# LSP: Add semantic token support for .att and .mtt template files

## Summary
Add Language Server Protocol support for mlld template files (.att and .mtt), providing semantic tokens for syntax highlighting.

## Background

mlld has two template file types:
- `.att` - "At Template" - uses `@var` interpolation (like `::...::` templates)
- `.mtt` - "Mustache Template" - uses `{{var}}` interpolation (like `:::...:::` templates)

Both support:
- `/for @item in @collection` ... `/end` loops
- `/when @condition` ... `/end` conditionals
- `<file.md>` file references
- `>>` and `<<` comments

Currently the LSP only supports `.mld` and `.mld.md` files.

## Implementation

### 1. Register File Extensions
In `cli/commands/language-server-impl.ts`, update `getModeFromUri`:
```typescript
function getModeFromUri(uri: string): MlldMode {
  const path = uri.replace('file://', '');
  if (path.endsWith('.mld.md') || path.endsWith('.mlld.md')) {
    return 'markdown';
  }
  if (path.endsWith('.mld') || path.endsWith('.mlld')) {
    return 'strict';
  }
  if (path.endsWith('.att') || path.endsWith('.mtt')) {
    return 'template'; // New mode for template files
  }
  return 'markdown';
}
```

### 2. Add Template Mode to Parser
Check if grammar already supports parsing template files. If not:
- Templates are simpler - mainly text with interpolation and control flow
- May need a `startRule: 'Template'` option

### 3. Create Template Visitors
Template files have different syntax than full mlld:
- No `/var`, `/exe`, `/import`, etc. - just content with interpolation
- Control flow: `/for`, `/when`, `/end`
- File references: `<file.md>`

Options:
a) Reuse existing visitors with template context
b) Create specialized `TemplateFileVisitor`

### 4. Handle Interpolation Differences
- `.att` files: tokenize `@var` as interpolation
- `.mtt` files: tokenize `{{var}}` as interpolation

The visitor needs to know which template type based on extension.

### 5. Update Document State
Store template type in DocumentState:
```typescript
interface DocumentState {
  uri: string;
  mode: MlldMode;
  templateType?: 'att' | 'mtt'; // NEW
  // ...
}
```

## Files to Modify
- `cli/commands/language-server-impl.ts` - Extension detection, document state
- `cli/commands/language-server.ts` - DocumentState interface
- `services/lsp/visitors/` - Template handling (new or modified visitors)
- Grammar may need template parsing support

## Testing
```bash
# Create test files
echo '@name said: @message' > tmp/test.att
echo '{{name}} said: {{message}}' > tmp/test.mtt

npm test services/lsp/semantic-tokens.test.ts
```

## Acceptance Criteria
- [ ] .att files recognized by LSP
- [ ] .mtt files recognized by LSP
- [ ] @var in .att highlighted as interpolation
- [ ] {{var}} in .mtt highlighted as interpolation
- [ ] /for, /when, /end highlighted as keywords
- [ ] <file.md> highlighted as file reference
- [ ] Comments >> and << highlighted

## Depends On
- Check if grammar supports template file parsing
- May need grammar work first

## Notes

## Better Approach Discovered

The grammar already has TemplateBodyAtt and TemplateBodyMtt as allowed start rules!

### Key Discovery
In build-grammar.mjs lines 128-140, these are already in allowedStartRules:
- TemplateBodyAtt (for .att files)
- TemplateBodyMtt (for .mtt files)

### Verified Working
parseSync(content, { startRule: 'TemplateBodyAtt' }) returns flat array with CORRECT LOCATIONS. No wrapping needed!

### Implementation Plan
1. Extend getModeFromUri() to detect .att/.mtt extensions
2. Add templateType to DocumentState
3. Update analyzeDocument() to use appropriate startRule
4. Semantic tokens work as-is (compatible AST nodes)

### Template Feature Differences
- ATT (.att): @var interpolation, /for loops, /when conditionals, <file.md> references, comments
- MTT (.mtt): {{var}} interpolation only (simpler format)

### Why This Works
- No wrapping/unwrapping needed
- No location adjustment needed
- Direct flat array of template parts
- Existing visitors handle the node types


