---
id: mlld-27r5
status: closed
deps: []
links: []
created: 2026-01-11T23:17:29.902983-08:00
type: task
priority: 0
---
# Ternary operator doesn't parse with method calls

## Summary
Ternary expressions fail to parse when the true branch contains a method call.

## Reproduction
```mlld
var @tier = "1,2"
var @tiers = @tier ? @tier.split(",") : []
>> Parse error: Text content not allowed in strict mode
```

## Expected
Should parse and evaluate to `["1", "2"]`

## Workaround
Use when-first instead:
```mlld
var @empty = []
var @tiers = when first [@tier => @tier.split(","); * => @empty]
```


