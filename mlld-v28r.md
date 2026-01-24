---
id: mlld-v28r
status: closed
deps: []
links: []
created: 2025-12-20T01:19:13.470036-08:00
type: bug
priority: 1
---
# LSP shows errors on wrong lines due to peggy backtracking

When LSP detects syntax error like `var @x = @y += @z`, it shows squiggles on WRONG lines (cascading to earlier `for...when` lines). Root cause: peggy's peg$maxFailPos tracks furthest position during backtracking. Fix: add explicit error recovery rule in grammar/directives/var.peggy to catch augmented assignment in var context early.


