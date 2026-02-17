---
id: mlld-4els
status: closed
deps: []
links: []
created: 2026-01-04T13:54:23.811618-08:00
type: bug
priority: 3
tags: [size-m, complexity-l, risk-l, impl-none]
updated: 2026-02-13T16:11:24Z
---
# Grammar: for block with inline => multiline template fails to parse

## Reproduction

This parses fine:
```mlld
for @r in @results [
  let @gapList = 1
  => `### @r.file
notes
`
]
```

But add a `when` clause and it fails:
```mlld
for @r in @results when @r.gaps.length > 0 [
  let @gapList = for @g in @r.gaps => `  - @g`
  => `### @r.file
@r.notes
`
]
```

Error:
```
Parse error: Syntax error: Invalid variable syntax, but found "=" at line X
```

## Expected
Both should parse - the `=>` on a new line inside a block should be recognized as the return statement.

## Workaround
Define the template in a let, then return it:
```mlld
for @r in @results when @r.gaps.length > 0 [
  let @gapList = for @g in @r.gaps => `  - @g`
  let @out = `### @r.file
@r.notes
`
  => @out
]
```


