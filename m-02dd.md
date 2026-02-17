---
id: m-02dd
status: closed
deps: []
created: 2026-02-15T22:28:38Z
type: feature
priority: 1
assignee: Adam
external-ref: qa-2026-02-15-2
updated: 2026-02-15T22:41:14Z
---
# Add string concatenation to + operator

Currently + is arithmetic-only. Agents consistently expect string concat. Add type-aware behavior: if either operand is string, concatenate; otherwise arithmetic. Must preserve taint from both operands. See QA run 2026-02-15-2 mcp-security experiments 02,03,09,16.


**2026-02-15 22:41 UTC:** Replaced by m-1001 (meaningful error instead of adding concat)
