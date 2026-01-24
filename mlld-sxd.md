---
id: mlld-sxd
status: closed
deps: []
links: []
created: 2025-12-10T21:10:55.463436-08:00
type: feature
priority: 2
---
# for-when filter syntax with implicit skip

**New syntax for filter-map in one step:**

```mlld
>> Dense block form
/var @positive = for @x in @xs when [
  @x > 0 => @x
  @x > 10 => "big"
]

>> One-liner  
/var @positive = for @x in @xs when @x > 0 => @x

>> With transform
/var @labels = for @x in @xs when @x > 0 => "positive: @x"
```

**Behavior changes:**
1. `for...when` implicitly skips non-matches (no nulls in result)
2. No need for `none => skip` boilerplate
3. The one-liner form `for @x when @cond => expr` works naturally since bare `when` already supports it

**Current behavior (to be changed):**
```mlld
/var @arr = for @x in @xs [
  when [ @x > 0 => @x ]  
]
>> Returns [1, null, 2, null, 3] - nulls for non-matches

/var @arr = for @x in @xs [
  when [ @x > 0 => @x; none => skip ]
]
>> Returns [1, 2, 3] - requires explicit skip
```

**New behavior:**
```mlld
/var @arr = for @x in @xs when [ @x > 0 => @x ]
>> Returns [1, 2, 3] - implicit skip, no nulls
```

This is the common case - nobody wants nulls in their collected array.


