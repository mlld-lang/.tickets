---
id: m-df79
status: closed
deps: []
links: []
created: 2026-01-25T21:18:34Z
type: bug
priority: 1
assignee: Adam Avenir
tags: [interpreter, for-loop, parallel]
---
# @mx.for.index not available in for parallel loops

Accessing `@mx.for.index` (or `@mx.for.total`) in a `for parallel` loop causes the error:

```
Field "index" not found in object
```

## Reproduction

```mlld
var @items = ["a", "b", "c"]

var @results = for parallel(2) @item in @items [
  let @idx = @mx.for.index + 1
  show \`Processing @idx of @mx.for.total\`
  => { item: @item, idx: @idx }
]
```

## Expected

`@mx.for.index` should be available and contain the current loop index.

## Workaround

Don't use `@mx.for.index` in parallel loops - track progress using other means.

