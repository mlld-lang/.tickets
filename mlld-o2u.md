---
id: mlld-o2u
status: closed
deps: []
links: []
created: 2025-12-09T08:30:53.814723-08:00
type: task
priority: 0
parent: mlld-a03
---
# Regex syntax highlighting: strict vs markdown

## Summary
Update TextMate/regex-based syntax highlighting to handle strict and markdown modes, plus add support for template files (.att, .mtt).

## Background

mlld has two highlighting mechanisms:
1. **Semantic Tokens (LSP)** - AST-based, context-aware, preferred
2. **TextMate Grammar** - Regex-based, fallback for non-LSP editors

TextMate grammars provide basic highlighting when LSP isn't available.

## Current TextMate Files
```
editors/vscode/syntaxes/
├── mlld.tmLanguage.json           # Main grammar for .mld/.mlld
├── mlld-markdown.tmLanguage.json  # Markdown mode
├── mlld-markdown.injection.json   # Injection grammar
└── mlld-minimal.tmLanguage.json   # Minimal fallback
```

## Tasks

### 1. Mode-Aware Directive Patterns
Update `mlld.tmLanguage.json` to make `/` optional in directive patterns:

```json
{
  "match": "^\\s*(/?)(?:var|show|exe|run|for|when|while|stream|guard|import|export|output|append|log|path)\\b",
  "name": "keyword.directive.mlld"
}
```

This allows both `var @x = 1` and `/var @x = 1` to highlight.

### 2. Add rc78 Keywords
Add new keywords to directive patterns:
- `while`, `done`, `continue`
- `stream`
- `guard` (with `before`, `after`, `always`, `allow`, `deny`, `retry`)
- `let` (in block contexts)
- `append`

### 3. Add .att Template File Support
Create `mlld-att.tmLanguage.json` for `.att` files (double-colon template syntax):
- `@var` interpolation
- `/for`, `/when`, `/end` directives
- `<file.md>` file references
- Comments `>>` and `<<`

Register in `package.json`:
```json
{
  "id": "mlld-att",
  "aliases": ["mlld Template (ATT)"],
  "extensions": [".att"],
  "configuration": "./language-configuration.json"
}
```

### 4. Add .mtt Template File Support
Create `mlld-mtt.tmLanguage.json` for `.mtt` files (triple-colon template syntax):
- `{{var}}` interpolation (NOT @var)
- `/for`, `/when`, `/end` directives
- `<file.md>` file references
- Comments `>>` and `<<`

Register in `package.json`:
```json
{
  "id": "mlld-mtt",
  "aliases": ["mlld Template (MTT)"],
  "extensions": [".mtt"],
  "configuration": "./language-configuration.json"
}
```

## Implementation Notes

### ATT Interpolation Pattern
```json
{
  "match": "@[a-zA-Z_][a-zA-Z0-9_]*",
  "name": "variable.other.mlld"
}
```

### MTT Interpolation Pattern
```json
{
  "begin": "\\{\\{",
  "end": "\\}\\}",
  "name": "meta.interpolation.mlld",
  "patterns": [
    { "match": "[a-zA-Z_][a-zA-Z0-9_]*", "name": "variable.other.mlld" }
  ]
}
```

### Template Directive Pattern (both)
```json
{
  "match": "^\\s*/(?:for|when|end)\\b",
  "name": "keyword.control.mlld"
}
```

### File Reference Pattern (both)
```json
{
  "begin": "<",
  "end": ">",
  "name": "string.other.link.mlld"
}
```

## Files to Modify/Create
- `editors/vscode/syntaxes/mlld.tmLanguage.json` - Add rc78 keywords, optional slash
- `editors/vscode/syntaxes/mlld-att.tmLanguage.json` - NEW
- `editors/vscode/syntaxes/mlld-mtt.tmLanguage.json` - NEW
- `editors/vscode/package.json` - Register new languages

## Testing
1. Disable LSP (semantic tokens)
2. Open .mld file - verify bare directives highlight
3. Open .att file - verify @var interpolation
4. Open .mtt file - verify {{var}} interpolation

## Acceptance Criteria
- [ ] Bare directives highlight in strict mode
- [ ] rc78 keywords (while, stream, let, etc.) highlight
- [ ] .att files: @var, /for, /when, /end, <file> highlight
- [ ] .mtt files: {{var}}, /for, /when, /end, <file> highlight
- [ ] package.json registers .att and .mtt extensions


