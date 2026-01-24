---
id: mlld-x0dg
status: open
deps: []
links: []
created: 2026-01-11T16:41:27.610741-08:00
type: task
priority: 2
---
# Standardize on .mx namespace for all file/content metadata

**Decision**: All metadata access should go through `.mx` namespace. Remove sugar paths that allow direct access.

**Standard pattern**:
```mlld
var @file = <README.md>
show @file.mx.filename   >> file name
show @file.mx.relative   >> relative path  
show @file.mx.tokens     >> token count
show @file.mx.fm.title   >> frontmatter field
show @file.mx            >> dump all available metadata (nice for discovery)
```

**Remove/don't document**:
- `.fm` directly on root (use `.mx.fm`)
- Any other direct metadata access sugar

**Benefits**:
1. Single namespace - no confusion
2. Discoverable - `show @var.mx` lists what's available
3. Clear separation - content vs metadata
4. Extensible - add fields without polluting root

**Work needed**:
1. Audit docs for any `.fm` usage, change to `.mx.fm`
2. Audit tests for direct metadata access patterns
3. Update howto file-loading-metadata (already shows .mx.fm)
4. Consider: should direct sugar throw error or just not be documented?
5. Ensure `show @var.mx` produces useful directory output

**Related**: mlld-cpuu (.fm doesn't work on loop vars) - fix should use .mx.fm pattern


