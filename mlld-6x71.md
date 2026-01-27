---
id: mlld-6x71
status: closed
deps: [mlld-9uju]
links: []
created: 2026-01-22T16:29:10.089065-08:00
type: task
priority: 1
parent: mlld-4z2w
---
# Phase 2c: Tool reshaping (bind, expose)

## Goal

Implement tool reshaping: pre-bind parameters (currying) and expose parameter subsets.

## Context

External APIs often have many parameters. Reshaping lets you:
- Pre-fill common values (owner, repo, etc.)
- Expose only what the agent needs
- Simplify tool interfaces

```mlld
>> Original exe has many params
exe @createGithubIssue(owner, repo, title, body, labels, assignees) = [...]

>> Reshaped tool binds owner/repo, exposes title/body only
var tools @agentTools = {
  createIssue: {
    mlld: @createGithubIssue,
    bind: { owner: "myorg", repo: "myrepo" },
    expose: ["title", "body"]
  }
}

>> Agent sees: createIssue(title, body)
>> Actual call: @createGithubIssue("myorg", "myrepo", title, body, null, null)
```

## Integration Points

1. **ToolDefinition**: Already has `bind` and `expose` fields (from 2a)
2. **SchemaGenerator**: Generate schema from exposed params only
3. **FunctionRouter**: Merge bound params before invocation
4. **Validation**: Ensure bound + exposed covers required params

## Schema Generation

```typescript
// Original exe: (owner, repo, title, body, labels, assignees)
// bind: { owner, repo }
// expose: [title, body]

// Generated schema:
{
  name: "create_issue",
  inputSchema: {
    properties: {
      title: { type: "string" },
      body: { type: "string" }
    },
    required: ["title", "body"]
  }
}
```

## Invocation Flow

```
MCP call: create_issue({ title: "Bug", body: "Details" })
    │
    ▼
FunctionRouter.executeFunction
    │
    ▼
Lookup tool def → get bind/expose
    │
    ▼
Merge: { owner: "myorg", repo: "myrepo", title: "Bug", body: "Details" }
    │
    ▼
Call @createGithubIssue with merged args
```

## Exit Criteria

- [ ] `bind: {...}` pre-fills params
- [ ] `expose: [...]` limits visible params
- [ ] Schema reflects only exposed params
- [ ] Bound params merged at invocation
- [ ] Validation: bound + exposed covers required
- [ ] Test: reshaped tool works end-to-end

## Estimated Effort
~4 hours

## Parent Epic
mlld-4z2w

## Depends On
mlld-9uju (tool collection syntax)


