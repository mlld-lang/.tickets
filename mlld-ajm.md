---
id: mlld-ajm
status: closed
deps: []
links: [mlld-sus]
created: 2025-12-10T20:21:29.989668-08:00
type: bug
priority: 2
---
# let statement doesn't support template RHS inside blocks

**Repro:**
```mlld
/for @item in @items [
  let @x = template "alice.att"
  show @x
]
```

**Error:**
```
Parse error: Expected for block statement list but "l" found
```

**Expected:** `let` should accept `template "path.att"` as a valid RHS, same as `/exe` does.

Reported by @mllddev.1 in chat.


