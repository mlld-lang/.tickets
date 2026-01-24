---
id: mlld-21gy
status: closed
deps: []
links: []
created: 2026-01-02T23:16:36.456828-08:00
type: task
priority: 1
---
# Update fixture builder for strict/markdown mode distinction

The docs test extraction needs updating:

1. extract-doc-tests.mjs: ✅ DONE
   - Supports ```mlld (strict → .mld) and ```mlld:md (markdown → .md) fences
   - Updated hasDirective check to look for known directive keywords
   - Generates example.mld for strict blocks, example.md for markdown blocks

2. build-fixtures.mjs: ✅ Works correctly

STATUS: Awaiting final test pass. Run npm test to verify remaining doc test failures are resolved or skipped.


