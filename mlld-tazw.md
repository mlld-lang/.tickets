---
id: mlld-tazw
status: closed
deps: []
links: []
created: 2025-12-20T01:19:13.660249-08:00
type: bug
priority: 1
---
# let @result += @b shows squiggles in exe blocks

Two issues:
1. LSP shows squiggles on valid syntax `exe @concat(a, b) = [ let @result = @a; @result += @b; => @result ]`
2. Runtime bug: += returns [3,4] instead of [1,2,3,4] - the augmented assignment is replacing instead of concatenating

Grammar parses correctly (AST shows AugmentedAssignment node). Issue is in interpreter evaluation.


