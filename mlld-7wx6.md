---
id: mlld-7wx6
status: closed
deps: []
links: []
created: 2026-01-11T23:17:30.048971-08:00
type: task
priority: 0
---
# Empty array literal [] doesn't parse in when-first expressions

## Summary
Empty array literal `[]` causes parse error when used in when-first result position.

## Reproduction
```mlld
var @tiers = when first [@tier => @tier.split(","); * => []]
>> Parse error: Syntax error: Invalid variable syntax
```

## Expected
Should parse and return empty array when fallback case matches.

## Workaround
Assign empty array to variable first:
```mlld
var @empty = []
var @tiers = when first [@tier => @tier.split(","); * => @empty]
```


