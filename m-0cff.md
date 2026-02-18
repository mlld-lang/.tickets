---
id: m-0cff
status: closed
deps: [m-335e]
created: 2026-02-18T01:27:52Z
type: task
priority: 0
assignee: Adam Avenir
updated: 2026-02-18T06:51:56Z
tags: [docs, guards]
---
# Update mcp-guards.md and labels-overview.md for unified guard label model

These docs are mostly correct but need minor updates for the unified label matching model.

**mcp-guards.md:**
- Line 14: 'Guards use data-label filters (like for secret) to catch labeled data flowing to MCP calls' — still correct, but add note that bare labels now also match operation labels. guard before destructive catches both destructive-labeled data AND destructive-labeled exe calls.

**labels-overview.md:**
- Lines 61-64: Example uses guard @noSecretExfil before op:exe — could also show the bare-label approach (guard before secret checking @mx.op.labels) as an alternative
- Consider adding a brief note that the same label can appear on data (var secret @x) and operations (exe exfil @func), and guards match both

**Acceptance criteria:**
- Both docs reflect that bare labels match data and operations
- No breaking changes to existing correct content
- Examples show both guard-on-data-label and guard-on-operation-label approaches where appropriate

