---
id: mlld-45d
status: open
deps: []
links: []
created: 2025-12-17T17:58:16.983003-08:00
type: feature
priority: 4
tags: [size-s, complexity-m, risk-m, impl-none]
---
# Optimize @exists() for file paths to avoid loading content

Performance optimization for @exists() builtin.

**Current behavior:**
```mlld
@exists("large-file.json")    // Loads entire file just to check
@exists(<huge-dataset.csv>)    // Same - loads full content
```

**Optimization:**
For file path strings and file load expressions, use lightweight fs.exists() check instead of full content load:

```mlld
@exists("file.md")         // Use fs.exists(), no load
@exists("@base/dir/file")  // Same, after path resolution
@exists(<file.md>)          // Detect file load AST, use fs.exists()
```

**Keep current behavior for:**
- Non-file expressions: @exists(@obj.field)
- When unclear if it's a file: let evaluation happen

**Tradeoffs:**
- More complex implementation
- Special-casing file paths vs general expressions
- May not matter unless users check existence of many/large files

**Dependencies:**
- Requires mlld-bqc (base @exists() implementation) to be done first
- Only pursue if performance becomes an issue

This is a nice-to-have optimization, not critical functionality.


