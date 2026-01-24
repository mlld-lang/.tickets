---
id: mlld-0tpg
status: open
deps: []
links: []
created: 2026-01-11T16:33:51.968461-08:00
type: task
priority: 2
---
# Empty array literal [] doesn't parse in when-first action position

**Problem**: Returning an empty array `[]` from a when-first condition fails to parse.

**Fails**:
```mlld
exe @getTiers(filter) = when first [
  @filter => @filter.split(",")
  * => []
]
>> Parse error: Expected various tokens but end of input found
```

**Workaround**:
```mlld
var @empty = []
exe @getTiers(filter) = when first [
  @filter => @filter.split(",")
  * => @empty
]
>> Works!
```

**Other literals work fine**:
```mlld
exe @classify(x) = when first [
  @x > 0 => "positive"
  * => "zero or negative"
]
>> String literals work
```

**Impact**: Can't return empty arrays as default values without pre-defining a variable. Awkward for functions that filter/transform collections.

**Grammar investigation**: The when-first action position may not allow ArrayLiteral in its expression grammar. Check WhenExpressionAction rule.

**Discovered in**: qa.mld refactoring - tried to return empty array when no tier filter provided.


