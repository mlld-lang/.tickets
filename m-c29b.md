---
id: m-c29b
status: closed
deps: []
created: 2026-02-20T20:18:10Z
type: bug
priority: 1
assignee: Adam
updated: 2026-02-20T20:20:35Z
---
# mlld qs docs show 'var @merged = { ...@obj, "extra": 1 }' for object spread but var is immutable — should use let in a block instead


**2026-02-20 20:20 UTC:** Not a bug — var with object spread is valid syntax (creates a new object, not mutation). Testing revealed a real label propagation bug on this path — filed separately.
