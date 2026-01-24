---
id: mlld-ecy
status: closed
deps: []
links: []
created: 2025-12-11T09:49:06.410873-08:00
type: feature
priority: 2
---
# Support comments (>> and <<) inside block bodies

**Current:** Comments aren't allowed inside `[...]` blocks:
```mlld
/exe @func() = [
  let @x = 1        >> this comment causes parse error
  let @y = 2  << so does this
  => @x + @y
]
```

Parse error: "Expected ... but '#' found" or "Invalid variable syntax, but found '<'"

**Desired:** Comments should work inside blocks just like they work everywhere else:
```mlld
/exe @func() = [
  let @x = 1        >> start-of-line comment
  let @y = 2  << end-of-line comment
  => @x + @y  << return value
]

/for @item in @items [
  >> explain this step
  let @processed = @transform(@item)
  show @processed  << debug output
]

/when [
  let @check = @validate(@input)  >> validate first
  @check.valid => @process(@input)  << process if valid
  * => show "Invalid"  << fallback
]
```

**Use case:** Documenting complex block logic, especially for multi-step orchestration patterns where each step needs explanation.

**Grammar location:** Block statement parsing (exe blocks, for blocks, when blocks) needs to accept comment tokens.


