---
id: mlld-hat8
status: closed
deps: []
links: []
created: 2026-01-11T23:08:20.91502-08:00
type: task
priority: 0
---
# Negation ! does not work with method call results

## Summary
`!@arr.includes(x)` returns literal string "!false" instead of boolean true.

## Reproduction
```mlld
var @arr = ["a", "b"]
show `includes: @arr.includes("c")`     >> false
show `!includes: !@arr.includes("c")`   >> !false (should be true)
```

## Impact
Cannot use negation in for-when clauses with method calls:
```mlld
>> This fails - returns 0 results because "!false" is truthy string
var @filtered = for @x in @items when !@checked.includes(@x) => @x
```

## Workaround
Use == false instead of !:
```mlld
var @filtered = for @x in @items when @checked.includes(@x) == false => @x
```


