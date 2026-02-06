---
id: mlld-zigd
status: closed
deps: []
links: []
created: 2026-01-01T16:18:51.601368-08:00
type: bug
priority: 2
tags: [size-s, complexity-m, risk-m, impl-partial]
updated: 2026-01-31T09:01:20Z
---
# File load detection too aggressive for XML-like tags with dots/@

The file load detection in templates triggers on XML-like tags when attribute values contain `.`, `/`, `*`, or `@`.

**Reproduction:**
```mlld
>> This fails - dots in version string
var @test = ::<MLLD_GUIDE version="1.0.0">::

>> This also fails - @ for interpolation  
var @version = "1.0"
var @test = ::<MLLD_GUIDE version="@version">::

>> This works - no special chars
var @test = ::<MLLD_GUIDE name="test">::
```

**Expected:** XML-like tags should be recognized as plain text regardless of attribute content.

**Actual:** Tags with `.` or `@` in attributes trigger file load attempts.

**Root cause:** Detection rule checks for `.`, `/`, `*`, `@` anywhere inside angle brackets, not just in path-like positions.

**Workaround:** Use JS template literals for content with XML + interpolated attributes.

**Related:** mlld-wrle (P0) - `:::...:::` broken with large content

**Possible fixes:**
1. Smarter heuristic (e.g., require path-like structure, not just presence of chars)
2. Escape syntax for angle brackets in templates
3. Check if content looks like valid path before attempting load



**2026-01-31 09:01 UTC:** Fixed in grammar/patterns/file-reference.peggy:
1. Added underscore support in tag/attribute name regex: [a-zA-Z0-9_-]* 
2. Added XML attribute pattern detection to AngleBracketLiteral to accept XML tags with attributes even when they contain . * or @
3. Added newline check in FileReferenceInterpolation to prevent matching across lines

Added test cases in tests/cases/feat/alligator/:
- xml-attributes-in-templates: Tests double-colon templates with XML tags containing dots and @
- xml-attributes-special-chars: Tests var directives with XML tags containing dots and @

All 3 XML-related tests pass.
