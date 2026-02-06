---
id: m-b64e
status: closed
deps: []
created: 2026-01-29T20:57:22Z
type: feature
priority: 2
assignee: Adam
tags: [scope-grammar, risk-m, size-m]
updated: 2026-01-30T22:44:50Z
---
# Allow any valid mlld expression inside function call parentheses

Currently expressions like ternaries don't work inside function argument parentheses. Example: @dbg(@result ? "success" : "failed", "label") fails to parse. We should make any valid mlld expression work inside () for function args.

## Acceptance Criteria

- Ternary expressions work in function args
- Other compound expressions work in function args
- Parser updated to accept expression grammar inside ()

