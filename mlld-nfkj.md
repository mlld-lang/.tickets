---
id: mlld-nfkj
status: closed
deps: []
links: []
created: 2026-01-12T13:47:44.453681-08:00
type: bug
priority: 1
---
# JSON parsing inconsistency: glob vs single file loads

## Summary

Glob-loaded JSON files are not being parsed into objects, while single file loads work correctly. This causes silent failures when accessing properties like `@file.topic` on glob results.

## Discovery

Found while debugging QA polish flywheel Phase 3a (apply loop). The loop appeared to execute but `@buildApplyPrompt` calls silently failed. After extensive debugging, traced to:

```
FieldAccess: Field "topic" not found in LoadContentResult
baseValue: {"content":"{\n  \"experiment\": \"06-M-mixed-types-jsonl\"...
_jsonParsed: false
```

## Reproduction

```mlld
>> Single file - works
var @single = <@base/qa/comments/10-H-empty-comments/proposed-fix.json>
show @single.topic  >> outputs: comments

>> Glob pattern - fails silently
var @glob = <@base/qa/**/proposed-fix.json>
let @first = @glob[0]
show @first.topic  >> ERROR: Field "topic" not found
show @first._jsonParsed  >> false
show @first.content  >> raw JSON string
```

## Expected Behavior (needs clarification)

User notes:
1. Alligators (<>) may be text-only by design, requiring `.data` or `| @json` for parsed access
2. BUT behavior should be consistent between `<file.json>` and `<*.json>` globs
3. There may have been an attempt to auto-repair or throw errors when accessing JSON-like properties on unparsed content
4. Possible that single files auto-parse but globs don't due to where the original issue was encountered

## Impact

- QA polish flywheel Phase 3a completely broken
- Parallel loops fail silently (errors swallowed in parallel context)
- Very hard to debug - no error until property access deep in call chain

## Questions to Resolve

1. What IS the intended behavior? Text-only or auto-parse JSON?
2. Should glob and single file behave identically?
3. Should we error loudly when accessing JSON-like properties on unparsed LoadContentResult?
4. Do we need better docs regardless of implementation choice?

## Files to Investigate

- File loading in interpreter (glob vs single file code paths)
- LoadContentResult handling
- JSON auto-parse logic

## Test Files Created

- tmp/test-glob-json.mld (reproduction case)
- tmp/test-parallel-structure.mld (original failure reproduction)
- tmp/test-which-param.mld (isolation test that found the bug)


