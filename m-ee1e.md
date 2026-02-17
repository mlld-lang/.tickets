---
id: m-ee1e
status: closed
deps: []
created: 2026-02-11T04:11:29Z
type: bug
priority: 0
assignee: Adam
updated: 2026-02-12T14:51:58Z
---
# for...when guard only works with single-line yield, not block body

The `when` guard in a `for` loop works with single-line `=> expr` but fails with a multi-line `[block]` body, producing a 'when [...] is ambiguous' parse error.

Works:
```mlld
let @filtered = for @item in @list when @item.active => @item
```

Fails:
```mlld
let @results = for @entry in @slideMap when @entry.num < @slideNum [
  let @data = @loadSlide(@entry.num, @entry.name)
  when @data => `<slide>\n@data\n</slide>`
]
```

Workaround: Move the guard inside the block body:
```mlld
let @results = for @entry in @slideMap [
  when @entry.num >= @slideNum => null
  let @data = @loadSlide(@entry.num, @entry.name)
  when @data => `<slide>\n@data\n</slide>`
]
```

The workaround inverts the condition and is less readable. The for-when-guard pattern should work consistently whether the body is a single expression or a block.

IMPORTANT: When addressing this, evaluate the overall design of `if` and `when` system-wide to ensure we have integrity across all contexts. The if/when distinction (if = imperative blocks, when = value expressions) creates several ambiguity edges:
- `for ... when guard [block]` (this ticket)
- `when value [pattern match]` vs `when [condition list]` when value is boolean
- `if @cond => expr` being invalid (must use `when`)

These should be resolved as a cohesive design pass, not ad-hoc per case.

## Acceptance Criteria

1. `for @item in @list when @cond [block]` parses and executes correctly
2. No regressions in existing when/if patterns
3. Grammar changes reviewed holistically for if/when design integrity

