---
id: mlld-2obk
status: closed
deps: []
links: []
created: 2026-01-11T23:59:34.821411-08:00
type: feature
priority: 3
---
# JSON.stringify equivalent for object-to-string conversion

## Summary
No native way to convert an object to a JSON string for interpolation.

## Use case
```mlld
let @issues = @result.issues ? `@result.issues` : "[]"
```

This works for simple cases but if @result.issues is an object/array, interpolation may not produce valid JSON.

## Desired
```mlld
let @issuesJson = @result.issues.toJson()
>> or
let @issuesJson = @json(@result.issues)
```

Would help when building prompts that need structured data as JSON strings.


