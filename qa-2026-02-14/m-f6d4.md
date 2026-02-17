---
id: m-f6d4
status: closed
deps: []
created: 2026-02-15T15:49:18Z
type: task
priority: 3
assignee: Adam
tags: [qa-2026-02-14, p3, code-cleanup, pipelines]
updated: 2026-02-15T17:14:33Z
---
# Code cleanup: m-f8e0 null sentinel conflation in parseStructuredJson

parseStructuredJson returns null for both 'parse failed' and 'parsed null value'. Caller uses if (parsed !== null) guard which silently drops null as a valid pipeline value.

Should return discriminated type: { parsed: true, value: null } vs null for failure.

## Acceptance Criteria

null values pass through pipelines correctly

