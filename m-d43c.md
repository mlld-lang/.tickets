---
id: m-d43c
status: closed
deps: []
created: 2026-02-24T20:46:05Z
type: chore
priority: 0
assignee: Adam Avenir
updated: 2026-02-25T00:57:15Z
---
# Update env config docs with enforcement scope table

## Task

Add an enforcement scope table to `docs/src/atoms/security/env-config.md` that distinguishes which env fields are enforced locally by mlld vs which require a container provider.

## What to Add

Add a table near the top of the file (after the initial field overview) with this content:

| Field | Enforced locally by mlld? | Notes |
|-------|--------------------------|-------|
| `tools` | Yes | mlld restricts available tools |
| `mcps` | Yes | mlld restricts available MCP servers |
| `fs` | No — requires container provider | mlld passes config but cannot enforce filesystem restrictions without a sandbox |
| `net` | No — requires container provider | mlld passes config but cannot enforce network restrictions without a sandbox |
| `limits` | No — requires container provider | mlld passes config but cannot enforce resource limits without a sandbox |

## Files to Edit

- `docs/src/atoms/security/env-config.md` — add the enforcement scope table
- `docs/src/atoms/security/env-overview.md` — add a one-line note pointing readers to the enforcement scope table in env-config

Do NOT edit any other files.

