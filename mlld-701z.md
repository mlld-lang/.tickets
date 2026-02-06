---
id: mlld-701z
status: closed
deps: []
links: [mlld-19f8]
created: 2026-01-11T14:23:07.189153-08:00
type: task
priority: 2
tags: [size-s, complexity-s, risk-xs, impl-done]
updated: 2026-01-30T06:46:53Z
---
# Document parallel syntax for for loops (parallel modifier not 'with' option)

Users attempt 'for @x in @items [...] with { parallel: 10 }' but correct syntax is 'for parallel(10) @x in @items [...]'. Need docs/howto coverage and possibly better error messages.


