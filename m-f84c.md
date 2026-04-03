---
id: m-f84c
status: closed
deps: []
links: []
created: 2026-03-24T04:17:48Z
type: bug
priority: 2
assignee: Adam
tags: [file-loading, section-selector]
updated: 2026-03-24T04:27:38Z
---
# Brace-syntax section selector on Python files returns JSON metadata instead of content

When using brace-syntax section selector on a Python file to extract a class:

```mlld
var @section = <@root/tests/test_new_modules.py { TestTier2IBANSubstitution }>
```

Expected: returns the class source code as a string (like `#` selector does for markdown headings).

Actual: returns a JSON array with metadata objects:
```json
[
  {
    "name": "TestTier2IBANSubstitution",
    "code": "class TestTier2IBANSubstitution:\n...",
    "type": "class",
    "line": 379,
    "mx": { "name": "TestTier2IBANSubstitution", "type": "class", "line": 379 }
  }
]
```

The content is there in `.code` but the caller has to know to unwrap it. For consistency with other selectors, `{ ClassName }` on a Python file should return the source code string directly.

Workaround: `@section[0].code`


## Notes

**2026-03-24T04:27:38Z** Fixed in orchestrator.ts — AST brace selector now sets .text to code content from results. Metadata still accessible via .data fields ([0].name, [0].mx.type, etc.)
