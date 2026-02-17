---
id: m-7ad1
status: closed
deps: [m-7be5]
created: 2026-02-15T08:11:14Z
type: chore
priority: 3
assignee: Adam
tags: [qa-2026-02-14, p3, docs, mcp]
updated: 2026-02-15T10:59:17Z
---
# Docs: mcp-guards fix @mx.op.labels.includes() example, document @mx.try indexing

Docs show @mx.op.labels.includes('destructive') as recommended pattern but it doesn't work (see m-7be5). @mx.try indexing unclear (0-indexed? counts initial attempt?). No retry example.

QA topic: mcp-guards

## Acceptance Criteria

Doc uses working guard patterns, documents @mx.try semantics, includes retry example

