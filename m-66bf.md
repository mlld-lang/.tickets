---
id: m-66bf
status: closed
deps: [mlld-69qu]
links: []
created: 2026-01-24T04:45:03Z
type: task
priority: 1
assignee: Adam Avenir
parent: mlld-4z2w
---
# Phase 2e: Guard-label integration test

## Goal

End-to-end test proving tool labels flow through MCPServer → FunctionRouter → guards → policy.

## Context

mlld-69qu adds labels to operation context, but no acceptance criteria verify the full path works. This task ensures the security invariant holds.

## Test Scenarios

```mlld
var tools @testTools = {
  safeRead: { mlld: @readData },
  dangerousDelete: { 
    mlld: @deleteData,
    labels: [destructive]
  }
}

guard @blockDestructive before op:exe = when [
  @mx.op.labels.includes("destructive") => deny "Blocked"
  * => allow
]

// Test 1: safeRead succeeds
// Test 2: dangerousDelete blocked by guard
// Test 3: Label visible in @mx.op.labels
// Test 4: Policy label rules also see tool labels
```

## Exit Criteria

- [ ] MCP call with labeled tool populates @mx.op.labels
- [ ] Guard can deny based on tool label
- [ ] Policy `labels: { "destructive": { deny: [...] } }` works
- [ ] Labels propagate through FunctionRouter → guard hooks
- [ ] Test covers: allow path, deny path, policy path

## Estimated Effort
~2 hours

## Depends On
mlld-69qu (tool labels in guard context)

