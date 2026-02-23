---
id: m-e3f2
status: closed
deps: []
created: 2026-02-22T17:20:37Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [error-handling]
updated: 2026-02-22T18:48:46Z
---
# Replace @base with @root in all error messages and suggestions

`@root` is the preferred alias for the project root path. `@base` is a supported alias but should not be the one we suggest in error messages.

## Changes needed

Replace `@base` with `@root` in these user-facing strings:

1. `interpreter/env/ImportResolver.ts:280` — error message "Use @base/..." → "Use @root/..."
2. `interpreter/env/ImportResolver.ts:324` — suggestion generation builds `@base/path` → build `@root/path`
3. `interpreter/eval/content-loader/orchestrator.ts:192` — hint mentions `@base/` prefix → `@root/` prefix
4. `interpreter/utils/interpolation.ts:855` — hint mentions `@base/` prefix → `@root/` prefix
5. `interpreter/utils/interpolation.ts:905` — hint mentions `@base/` prefix → `@root/` prefix
6. `interpreter/eval/import/ImportTypePolicy.ts:114` — reorder "@base/@root" → "@root/@base"
7. `interpreter/eval/import/ImportTypePolicy.ts:135` — reorder "@base/@root" → "@root/@base"

Also update test assertions that match `@base` in error output:
- `interpreter/path-resolution-suggestions.test.ts` (lines 33, 45, 58)
- `interpreter/fuzzy-local-imports.test.ts` (line 177)

