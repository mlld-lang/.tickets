---
id: m-f73a
status: closed
deps: []
created: 2026-02-15T08:11:07Z
type: chore
priority: 3
assignee: Adam
tags: [qa-2026-02-14, p3, docs, guards]
updated: 2026-02-15T11:14:59Z
---
# Docs: security-guard-composition missing always timing, transform semantics, retry scope

always timing not mentioned despite taking precedence. Transforms don't chain (last wins) but not stated. Retry is pipeline-only but not noted. Before guards on op:exe have limitations.

QA topic: security-guard-composition

## Acceptance Criteria

Doc covers always timing, transform last-wins behavior, retry scope, op:exe warnings

