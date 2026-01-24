---
id: mlld-72cv
status: closed
deps: []
links: []
created: 2026-01-14T11:44:44.591343-08:00
type: feature
priority: 1
---
# Add support for when @condition [block] syntax

Currently `when @condition [block]` doesn't work - requires `=>` after condition, and even `when @condition => [block]` doesn't allow `let` inside the block (only show, var, output, run, etc).

This is inconsistent with `for [...] [block]` and `exe @x = [block]` which both allow full statement support including `let`.

Proposed: Add `when @condition [block]` where block actions are identical to what you can do in an exe or for block.

Example that should work:
```mlld
when @needsMerge.length > 0 [
  let @mergePrompt = @buildMergePrompt(...)
  show \`Running merge phase...\`
  let @mergeResponse = @claude(...)
]
```

Currently requires awkward workarounds like:
```mlld
when @runPhase1 => show \`Phase 1...\`
when @runPhase1 => show \`More output\`
when @runPhase1 => show \`Even more\`
```


