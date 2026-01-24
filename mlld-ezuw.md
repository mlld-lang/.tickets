---
id: mlld-ezuw
status: open
deps: []
links: []
created: 2026-01-11T16:34:41.902442-08:00
type: task
priority: 2
---
# Ternary operator doesn't work in let assignments inside exe blocks

**Problem**: Can't use ternary operator `? :` for conditional assignment in `let` statements inside exe blocks.

**Fails**:
```mlld
exe @process(filter) = [
  let @tiers = @filter ? @filter.split(",") : []
  >> Parse error on the '?'
  => @tiers
]
```

**Also fails with when first**:
```mlld
exe @process(filter) = [
  let @tiers = when first [
    @filter => @filter.split(",")
    * => []
  ]
  >> Parse error - when first not allowed in let RHS
  => @tiers
]
```

**Workarounds**:

1. Define helper exe outside:
```mlld
exe @getTiers(f) = when first [
  @f => @f.split(",")
  * => @empty
]
exe @process(filter) = [
  let @tiers = @getTiers(@filter)
  => @tiers
]
```

2. Move all logic to return:
```mlld
exe @process(filter) = when first [
  @filter => @filter.split(",")
  * => []
]
```

**Impact**: Can't build up intermediate conditional values inside exe blocks. Forces either helper functions or restructuring logic.

**Grammar investigation**: ExeBlockStatement's let assignment RHS may have a restricted expression grammar that doesn't include TernaryExpression or WhenExpression.

**Discovered in**: qa.mld refactoring - tried to conditionally parse tier filter inside exe block.


