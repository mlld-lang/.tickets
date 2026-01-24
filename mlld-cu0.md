---
id: mlld-cu0
status: open
deps: []
links: []
created: 2025-12-10T20:59:07.161018-08:00
type: feature
priority: 2
---
# Support semicolon separation for when arms (universal block separator)

**Current:** When arms require newlines:
```mlld
/var @arr = for @x in @xs => when [
  @x == 1 => @x
  none => skip
]
```

**Desired:** Semicolons should work as universal block separators:
```mlld
/var @arr = for @x in @xs [ when [ @x == 1 => @x; none => skip ] ]
```

Currently fails with: "Syntax error: Invalid variable syntax, but found ";""

Other blocks support semicolon separation - this should be universal for consistency and one-liner convenience.


