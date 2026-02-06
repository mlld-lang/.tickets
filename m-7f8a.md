---
id: m-7f8a
status: closed
deps: []
links: []
created: 2026-01-28T23:46:02Z
type: task
priority: 1
parent: 
assignee: Adam
tags: [docs, doc-fix, security]
updated: 2026-01-31T08:36:04Z
---
# Clarify 'denied' vs 'deny' keyword semantics

Docs use 'denied' but may mean 'deny'. Clarify whether this is a typo or distinct concept in security-denied-handlers docs.


**2026-01-31 08:36 UTC:** Clarified distinction: 'deny' is a guard action, 'denied' is a when-condition. Updated docs/src/atoms/security/denied-handlers.md, docs/llm/llms-security.txt, and docs/user/security.md.
