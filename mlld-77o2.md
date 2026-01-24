---
id: mlld-77o2
status: closed
deps: []
links: []
created: 2025-12-21T21:04:39.616423-08:00
type: bug
priority: 1
---
# Template interpolation leaks needsInterpolation marker in object literal values

When a string value in an object literal contains an @reference (like 'Hello @party'), the value shows as raw JSON {needsInterpolation: true, parts: [...]} instead of the interpolated string.

Root cause: DataString returns { needsInterpolation: true, parts } marker but PrimitiveEvaluator doesn't handle this shape - only handles { wrapperType, content }.

Reproduction:
/var @msg = { "body": "Hello @party" }
/show @msg.body

Expected: Hello @party (or resolved variable)
Actual: {"needsInterpolation":true,"parts":[...]}


