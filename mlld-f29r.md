---
id: mlld-f29r
status: open
deps: [mlld-4cw]
links: []
created: 2026-01-01T15:06:50.486032-08:00
type: task
priority: 4
---
# Docs: automated llms.txt validation and sync per release

Create mlld scripts that:
1. Validate llms.txt modules against current codebase (check examples still work, syntax is correct)
2. Auto-update version numbers on release
3. Flag stale/outdated sections based on changed features
4. Potentially extend to docs/user/ as well

Depends on llms.txt modularization being complete first.


