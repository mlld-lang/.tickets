---
id: mlld-tvnq
status: closed
deps: []
links: []
created: 2026-01-04T14:09:09.277912-08:00
type: bug
priority: 3
---
# Ternary operator not working in all contexts

## Issue

Ternary operator fails in some contexts:

```mlld
var @summary = @flatGaps.length == 0 ? "No gaps" : `Found @flatGaps.length gaps`
```

Error:
```
Text content not allowed in strict mode (.mld)
```

## Expected
Ternary should work anywhere an expression is expected.


