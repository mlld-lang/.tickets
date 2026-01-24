---
id: mlld-ijjl
status: closed
deps: []
links: []
created: 2026-01-03T03:13:50.248597-08:00
type: bug
priority: 2
---
# Parser: blocks require let before return (should allow direct return)

Blocks currently require at least one `let` statement before the return `=>`. This should not be required.

**Fails:**
```mlld
exe @test() = [
  => { name: "hello" }
]
```

**Works (with dummy let):**
```mlld
exe @test() = [
  let @x = 1
  => { name: "hello" }
]
```

The grammar should allow blocks with only a return statement.


