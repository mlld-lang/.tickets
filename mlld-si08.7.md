---
id: mlld-si08.7
status: closed
deps: [mlld-8o14]
links: []
created: 2026-01-19T22:25:03.695943-08:00
type: task
priority: 2
tags: [size-s, complexity-xs, risk-xs, impl-done]
parent: mlld-si08
updated: 2026-01-30T06:46:53Z
---
# .mx.tools namespace (toolcalls, allowed, denied)

## Goal

Implement the `.mx.tools` namespace from spec v4 Part 1.9.

## Syntax

```mlld
@mx.tools.calls      // Array of tool calls made this turn
@mx.tools.allowed    // Tools the LLM is permitted to use
@mx.tools.denied     // Tools explicitly denied
```

## Use Cases

### Enforce Verification

```mlld
guard @ensureVerified after llm = when [
  @mx.tools.calls.includes("verify") => allow
  * => retry "Must verify instructions before proceeding"
]
```

### Audit Tool Usage

```mlld
guard after llm = when [
  @mx.tools.calls.length > 0 => [
    log `Tools called: @mx.tools.calls`
    allow
  ]
  * => allow
]
```

### Restrict Tools Dynamically

```mlld
guard before llm = when [
  @input.mx.taint.includes("untrusted") => [
    // Restrict available tools for untrusted context
    allow with { tools: @mx.tools.allowed.filter(t => !t.match(/destructive/)) }
  ]
  * => allow
]
```

## Implementation Tasks

1. Track tool calls during LLM execution
2. Expose `@mx.tools.calls` array
3. Track allowed/denied tools from policy
4. Expose `@mx.tools.allowed` and `@mx.tools.denied`
5. Wire into guard context

## Exit Criteria

- [ ] `@mx.tools.calls` returns tool calls made
- [ ] `@mx.tools.allowed` returns permitted tools
- [ ] `@mx.tools.denied` returns denied tools
- [ ] Guards can check/filter tool calls

## References

- Spec v4 Part 1.9: The `.mx.tools` Namespace

## Estimated Effort

~4 hours



