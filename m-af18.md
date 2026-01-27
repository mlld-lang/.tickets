---
id: m-af18
status: closed
deps: []
links: []
created: 2026-01-25T21:03:43Z
type: bug
priority: 1
assignee: Adam Avenir
tags: [grammar, when, output]
---
# output directive with variable destination fails inside when blocks

The `output ... to @variable` pattern fails to parse inside `when` blocks, but works fine at top level or inside `for` blocks.

## Reproduction

```mlld
var @path = "test.txt"

>> This works:
output "hello" to @path

>> This fails with parse error:
when true [
  output "hello" to @path
]
```

## Error

```
expected: when action block body
found: 'o' (start of 'output')
```

## Notes

- `output ... to "literal.txt"` works fine inside when blocks
- `output @content to "literal.txt"` also works
- Only the `to @variable` destination pattern fails
- Discovered while writing llm/run/stale.mld

