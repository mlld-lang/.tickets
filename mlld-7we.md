---
id: mlld-7we
status: closed
deps: []
links: []
created: 2025-12-17T21:23:00.932589-08:00
type: bug
priority: 0
---
# Parser error: newline after bracket expression in exe block let statement

Parser error specific to /for blocks with bracket notation in let statements:

**Fails:**
```mlld
/exe @test(registry) = for @id in @registry.ctx.keys => [
  let @agent = @registry[@id]
  => @agent.tldr
]
```

**Works:**
```mlld
/exe @test(registry, id) = [
  let @agent = @registry[@id]  // Standalone exe block OK
  => @agent.tldr
]
```

The issue is specific to `for ... => [...]` block syntax, not general exe blocks.

Error: 'Expected end of input but "\n" found' at newline after bracket expression.

Blocking @partydev's multi-agent routing implementation.


