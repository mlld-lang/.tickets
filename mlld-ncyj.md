---
id: mlld-ncyj
status: open
deps: []
links: []
created: 2026-01-11T16:34:11.733656-08:00
type: task
priority: 3
tags: [size-s, complexity-xs, risk-xs, impl-none]
---
# No toString() or String() for type coercion

**Problem**: mlld has no way to explicitly convert a number to a string. No `toString()` method, no `String()` function.

**Fails**:
```mlld
var @num = 5
var @str = @num.toString()
>> Error: Method not found: toString on num
```

**Also not available**:
```mlld
var @str = String(@num)  >> String is not a valid mlld construct
```

**Workaround** (non-obvious):
```mlld
var @num = 5
var @str = \`@num\`  >> Template interpolation coerces to string
>> Works but not discoverable
```

**Use case**: Needed when comparing numbers to string arrays:
```mlld
var @tiers = "1,2".split(",")  >> ["1", "2"] - strings
var @fileTier = @file.fm.qa_tier  >> 1 - number
>> Need to convert @fileTier to string to use .includes()
@tiers.includes(\`@fileTier\`)  >> works with template workaround
```

**Suggestion**: Add `.toString()` to the builtin methods list, or document the template interpolation pattern in howto methods-builtin.

**Discovered in**: qa.mld refactoring - needed to check if numeric qa_tier was in a comma-separated string list.



**2026-01-30 22:44 UTC:** .text doesn't exist. May need @String() function or similar. Note: string interpolation already coerces to string, so `@num` works for most cases.
