---
id: m-fbc9
status: open
deps: []
created: 2026-02-17T04:56:38Z
type: task
priority: 0
assignee: Adam
---
# Audit @ escaping consistency across all interpolation contexts

Investigate all contexts where @ interpolation happens (backtick strings, double-quoted strings, single-quoted strings, ::..:: templates, .att files, cmd {} blocks) and ensure \@ and @@ escaping works identically in every one. The escaping-at doc previously listed per-context examples suggesting they might differ — verify they don't, or fix the grammar so they don't.

## Acceptance Criteria

Test coverage proving \@ and @@ produce literal @ in every interpolation context

