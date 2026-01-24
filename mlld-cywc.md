---
id: mlld-cywc
status: closed
deps: []
links: []
created: 2026-01-10T23:41:20.81788-08:00
type: bug
priority: 0
---
# Template interpolation fails in block let statements

When passing a template directly to a function call inside a block's let statement, the template AST nodes are passed raw instead of being interpolated first.

Example that fails:
```mlld
exe @hasSubstance(response) = [
  let @result = @haiku("Does this add value? @response")
  => @result.trim().toLowerCase().startsWith("yes")
]
```

The @response variable is NOT substituted - the raw AST is passed to @haiku.

Workaround - use intermediate variable:
```mlld
exe @hasSubstance(response) = [
  let @prompt = "Does this add value? @response"
  let @result = @haiku(@prompt)
  => @result.trim().toLowerCase().startsWith("yes")
]
```

This works because assigning to a variable triggers interpolation first.

Discovered while testing intro.md examples.


