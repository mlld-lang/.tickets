---
id: m-b4bd
status: open
deps: []
created: 2026-02-25T19:48:45Z
type: chore
priority: 0
assignee: Adam Avenir
---
# Fix @base/@root output path hack

output.ts:718-725 and ImportResolver.ts:184-188 use string prefix matching to resolve @base/@root instead of going through ProjectPathResolver. The resolver infrastructure already supports this — ProjectPathResolver just needs output:true and write:true in capabilities, plus output context handling in resolve(). Then both hacks can be replaced with proper resolver dispatch. Duplicate code violates DRY.

