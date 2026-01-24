---
id: mlld-u7yz
status: open
deps: []
links: []
created: 2026-01-11T20:40:38.975742-08:00
type: bug
priority: 2
---
# parallel(n) doesn't accept variable for concurrency limit

In for-parallel loops, the concurrency limit must be a literal number. Variable references like `for parallel(@limit) @x in @items` fail to parse.

**Reproduction:**
```mlld
var @limit = 5
var @results = for parallel(@limit) @item in @items => @process(@item)
```

**Error:** Parse error - expects literal number

**Workaround:** Use literal: `for parallel(5) @item in @items`

**Expected:** Should accept variable references for dynamic concurrency control.


