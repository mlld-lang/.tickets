---
id: m-ee10
status: closed
deps: []
links: []
created: 2026-02-23T04:52:54Z
type: bug
priority: 0
assignee: Adam
updated: 2026-02-23T05:00:21Z
---
# autosign should sign quoted strings and backtick templates, not just :: templates

autosign: ["templates"] only signs `:: ::` template literals. Quoted strings ("") and backtick templates (` `) are also templates — they interpolate variables — but get runtime type `simple-text` instead of `template`, so `isTemplateCategoryVariable` in `interpreter/eval/auto-sign.ts:90` skips them.

**Repro:**
```mlld
policy @p = { defaults: { autosign: ["templates"] } }
var @a = ::hello::        >> type: template   — SIGNED
var @b = `hello`          >> type: simple-text — NOT SIGNED
var @c = "hello"          >> type: simple-text — NOT SIGNED
```

All three are templates (they all interpolate @variables). All three should be signed.

**Root cause:** The var evaluator eagerly resolves `""` and backtick templates to plain strings, losing the template type. The AST has `inferredType: 'template'` for all three, but by the time `maybeAutosignVariable` runs, only `::` retains `variable.type === 'template'`.

**Fix options:**
1. Preserve template type through var evaluation for `""` and backtick sources (var evaluator change)
2. Check AST `inferredType` or source metadata in `isTemplateCategoryVariable` instead of only runtime type
3. Both — preserve type AND check metadata as defense in depth

**Impact:** Blocks the website guard-mcp-tools example from using simple quoted strings for signed instructions. Users writing `var @prompt = "Triage issues"` expect autosign to cover it.

## Acceptance Criteria

- `autosign: ["templates"]` signs quoted string vars that contain interpolation
- `autosign: ["templates"]` signs backtick template vars
- `autosign: ["templates"]` continues to sign :: templates
- All three syntaxes produce variables that pass `isTemplateCategoryVariable`

