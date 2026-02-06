---
id: m-fd2a
status: closed
deps: []
links: []
created: 2026-01-29T01:37:41Z
type: task
priority: 0
parent: 
assignee: Adam
tags: [docs, doc-fix, security, hallucination]
updated: 2026-01-31T06:49:09Z
---
# Remove 'guard privileged' syntax from label-modification docs

Human confirmed: 'guard privileged' syntax is hallucinated documentation. The keyword is 'privileged' in import statements (import guards privileged from ...) or as a flag on guard definitions, NOT as a guard declaration modifier. Remove examples showing 'guard privileged @name' from docs/src/atoms/security/label-modification.md lines 50, 56, 62, 95.

