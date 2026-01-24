---
id: mlld-c6we
status: closed
deps: []
links: []
created: 2026-01-11T21:01:58.410007-08:00
type: task
priority: 0
---
# JSON file fields not accessible in for-loop iterations

## Summary
When loading JSON files via glob, direct field access works but iteration access fails.

## Reproduction
```mlld
var @allResults = <@base/qa/**/results.json>
var @first = @allResults[0]

>> This works - returns "pass"
show `Direct: @first.status`

>> This returns all nulls
var @statuses = for @r in @allResults => @r.status
show `Via for: @statuses`  >> [null, null, null, ...]

>> This finds 0 results despite 65 actual failures
var @failed = for @r in @allResults when @r.status == "fail" => @r
show `Failed: @failed.length`  >> 0
```

## Root Cause
The foreach error reveals the issue - when iterating, the LoadContentResult objects have `_jsonParsed: false`. The JSON content is not being unwrapped/parsed in iteration context, but is parsed for direct access.

## Expected
`@r.status` in a for loop should work identically to `@first.status` direct access.


