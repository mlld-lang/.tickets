---
id: mlld-wmzl.14
status: closed
deps: [mlld-wmzl.3]
links: []
created: 2026-01-16T14:07:45.732329-08:00
type: task
priority: 1
parent: mlld-wmzl
---
# Phase 3.5: 'using' keyword (sugar for with { auth/using })

## Goal

Implement the `using` keyword as sugar for `with` config and credential injection.

## Syntax

```mlld
run cmd { claude } using auth:claude
run cmd { tool } using @token as TOOL_KEY
run cmd { tool } using @token as TOOL_KEY with { timeout: 30000 }
```

## Grammar Desugaring

`using` desugars into `with`:

- `using auth:name` → `with { auth: "name" }`
- `using @var as ENV` → `with { using: { var: "var", as: "ENV" } }`

`with` merges existing options.

## Types

`WithClause` adds:

```typescript
auth?: string;
using?: { var: string; as: string };
```

## Evaluation

- `auth`: look up `policy.auth[name]`, fetch from `from`, inject as `as`.
- `using`: resolve variable value, inject as env var.
- Both set `flowChannel: 'using'` so label flow checks skip interpolation rules.

## Exit Criteria

- [ ] Grammar parses `using auth:name` and `using @var as ENV`
- [ ] Desugars into `with` config and merges options
- [ ] Auth injection resolves policy.auth
- [ ] Explicit using injects variable value
- [ ] Tests cover both forms



