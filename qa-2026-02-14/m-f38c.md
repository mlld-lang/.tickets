---
id: m-f38c
status: closed
deps: []
created: 2026-02-15T08:10:27Z
type: task
priority: 2
assignee: Adam
tags: [qa-2026-02-14, p2, design, policy]
updated: 2026-02-15T08:44:22Z
---
# Design decision: op:exe in policy deny lists

op:exe works in guard syntax (before op:exe) but not in policy deny lists. Unclear if intentional.

QA topic: mcp-policy

## Acceptance Criteria

Behavior is decided and documented


**2026-02-15 08:44 UTC:** Confirmed intentional: policies use a fixed capability set (cmd, sh, network, js, py, node). There is no automatic op:exe label per spec Section 1.5. Guards are the user-extensible mechanism for custom filters.
