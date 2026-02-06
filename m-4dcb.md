---
id: m-4dcb
status: closed
deps: []
links: []
created: 2026-01-25T20:55:03Z
type: feature
priority: 3
assignee: Adam Avenir
tags: [grammar, for-loop, size-s, complexity-s, risk-s, impl-none]
updated: 2026-01-30T06:58:53Z
---
# Support variables in parallel() limit and timeout parameters

Currently `for parallel(N)` and `for parallel(N, timeout)` only accept literal values. Variables should be supported:

```mlld
var @limit = 10
var @timeout = "30s"
for parallel(@limit, @timeout) @item in @list [...]
```

Discovered while writing llm/run/stale.mld - had to hardcode `parallel(10)` instead of using a configurable variable.

