---
id: mlld-cpuu
status: closed
deps: []
links: []
created: 2026-01-11T16:33:22.417985-08:00
type: task
priority: 1
tags: [size-s, complexity-m, risk-m, impl-none]
updated: 2026-01-30T11:58:49Z
---
# .fm accessor doesn't work on for-loop iteration variables

**Problem**: When iterating over glob-loaded files, the `.fm` (or `.mx.fm`) accessor for frontmatter doesn't work on the loop variable, even though it works with direct indexing.

**Note**: `.fm` appears to be syntactic sugar for `.mx.fm` - both work on direct access. Docs show `.mx.fm` as the official pattern.

**Works**:
```mlld
var @files = <@base/docs/**/*.md>
var @first = @files[0]
show @first.fm.id     >> works
show @first.mx.fm.id  >> also works
```

**Fails** (both patterns):
```mlld
var @files = <@base/docs/**/*.md>
for @f in @files => show @f.fm.id     >> Error: Field 'fm' not found
for @f in @files => show @f.mx.fm.id  >> Error: Field 'fm' not found in object
```

**Debug output shows**: The loop variable has `_fmParsed: false` - frontmatter parsing is lazy and only triggered by direct property access on the array element, not when iterating.

**Impact**: Can't filter/map glob-loaded files by frontmatter fields using native mlld. Forces JS escape hatch:
```mlld
exe @extractTopics(files) = js {
  return files.filter(f => f.fm?.qa_tier).map(f => f.fm.id);
}
```

**Expected**: `@f.fm.id` or `@f.mx.fm.id` should trigger lazy frontmatter parsing just like `@files[0].fm.id` does.

**Investigation questions**:
1. Should we standardize on `.mx.fm` and remove `.fm` sugar?
2. Why does direct index access trigger lazy parsing but loop iteration doesn't?
3. Is the loop variable a different object type than the indexed element?

**Files to investigate**:
- Glob/file loading code that creates LoadContentResult
- For-loop iteration variable binding
- Lazy .fm / .mx.fm accessor implementation

**Discovered in**: qa.mld refactoring session - tried to replace JS with native for-when filtering.


