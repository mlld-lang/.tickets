---
id: mlld-pnc3
status: closed
deps: []
links: []
created: 2026-01-11T23:05:19.671552-08:00
type: task
priority: 2
---
# Documentation updates for LoadContentResult preservation and directory metadata

## Summary

Two recent changes require documentation updates:
1. **mlld-c6we fix**: LoadContentResult now preserved in for-loop iterations, making `.mx`, `.json`, `.fm` all accessible
2. **mlld-ls0t feature**: Added `.mx.dirname`, `.mx.relativeDir`, `.mx.absoluteDir` to file metadata

## Documentation Changes Needed

### 1. LLM Atom: `docs/src/atoms/syntax/file-loading-metadata.md`
- Add new directory properties to the example:
  - `.mx.dirname` - immediate parent directory name
  - `.mx.relativeDir` - relative path to containing directory
  - `.mx.absoluteDir` - absolute path to containing directory

### 2. User Docs: `docs/user/content-and-data.md`

**A. File Metadata section (~line 267-293)**
- Add the 3 new directory properties to the property list

**B. `.keep` section (~line 53-67)**
- Revise to clarify `.keep` is ONLY needed for JS/Node boundary
- Remove implication that it's needed for basic metadata access

**C. Gotchas - Metadata Access in Loops (~line 1122-1137)**
- **REMOVE or heavily revise** - this section is now WRONG
- Loop iteration now preserves LoadContentResult
- `.mx`, `.json`, `.fm` all work directly in loops
- The `.keep` pattern is NOT needed for loops

**D. Gotchas - Metadata in Pipelines (~line 1139-1152)**
- Keep but clarify - `.keep` still needed when passing to JS/Node stages

### 3. Dev Docs: `docs/dev/ALLIGATOR.md`
- Keep JS-related `.keep` examples
- Clarify that `.keep` is specifically for JS/Node boundary, not general use

### 4. Dev Docs: `docs/dev/DATA.md`
- References to `.keep` are accurate for JS/Node context - no changes needed

## Test verification
```mlld
var @files = <*.txt>
for @file in @files => show \`@file.mx.filename | @file.mx.dirname\`  # Works now
for @file in @files => show @file.json.status  # Works now
for @file in @files => show @file.fm.title     # Works now
```

## Related
- mlld-c6we (closed) - JSON/frontmatter fields fix
- mlld-ls0t (closed) - Directory metadata feature


