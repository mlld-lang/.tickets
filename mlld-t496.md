---
id: mlld-t496
status: open
deps: []
links: []
created: 2026-01-22T11:27:49.676834-08:00
type: feature
priority: 2
---
# Add @groupByFn to @mlld/array - group by executable result

Current @groupBy only accepts a key name string. Add @groupByFn that accepts an exe reference to compute the group key dynamically.

Example use case:
```mlld
exe @categorize(wt) = when first [
  @wt.main_state == "integrated" => "cleanup"
  @wt.main_state == "ahead" => "keep"
  * => "investigate"
]

var @groups = @groupByFn(@worktrees, @categorize)
// { cleanup: [...], keep: [...], investigate: [...] }
```

Implementation notes:
- Need to figure out how to pass exe references to JS
- Alternative: accept a key path expression that gets evaluated
- Taint: output inherits union of all input item taints


