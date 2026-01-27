---
id: m-75cc
status: open
deps: []
links: []
created: 2026-01-25T21:19:36Z
type: bug
priority: 1
assignee: Adam Avenir
tags: [interpreter, when, for-loop, stale-check-8]
---
# when block show output overrides for loop return value

When a `when` block inside a `for` loop contains a `show` statement, the show output is captured as the loop iteration's return value, overriding any explicit `=> { ... }` return statement.

## Reproduction

```mlld
var @items = [{val: 1}, {val: 5}]

var @results = for @item in @items [
  when @item.val > 3 [
    show \`big: @item.val\`
  ]
  when @item.val <= 3 [
    show \`small: @item.val\`
  ]
  => { item: @item, doubled: @item.val * 2 }
]

show \`Results: @results\`
```

## Output

```
Results: ["small: 1","big: 5"]
```

## Expected

```
Results: [{"item":{"val":1},"doubled":2},{"item":{"val":5},"doubled":10}]
```

The explicit `=> {...}` return should take precedence over show output in when blocks.

