---
id: m-0cd3
status: closed
deps: []
links: [m-2449]
created: 2026-02-23T04:39:59Z
type: bug
priority: 0
assignee: codex
tags: [security, env, labels]
updated: 2026-02-27T06:48:40Z
---
# Design: env block return expressions bypass tool collection label scope

When an exe is called as a direct return expression inside an env block (`=> @fn()`), tool collection labels from `env with { tools }` are not applied to the output. The workaround is to use an intermediate let binding: `let @r = @fn() / => @r`.

The issue is that the return expression evaluates in a different context than let bindings within the env block body. The scoped environment config (which carries the tool collection) may not be active at the point of invocation for direct return expressions.

This is a security-relevant edge case: if an agent exe uses `=> @toolCall()` instead of `let @r = @toolCall() / => @r`, the tool collection labels silently don't apply, creating a taint gap.

Repro:
```
exe @read(id: string) = \`data:@id\`
var tools @t = { read: { mlld: @read, labels: ["untrusted"] } }
exe @agent(tools) = env with { tools: @tools } [
  => @read("1")   -- untrusted NOT applied
]
```

vs:
```
exe @agent(tools) = env with { tools: @tools } [
  let @r = @read("1")  -- untrusted IS applied
  => @r
]
```

