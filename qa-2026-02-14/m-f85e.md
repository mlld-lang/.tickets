---
id: m-f85e
status: closed
deps: []
created: 2026-02-15T08:11:01Z
type: chore
priority: 3
assignee: Adam
tags: [qa-2026-02-14, p3, docs, security]
updated: 2026-02-15T11:20:42Z
---
# Docs: security-needs-declaration - unclear if enforced or advisory

needs {} is not enforced at runtime. Docs don't explain whether it's a linting hint or security control. Array vs bare syntax inconsistent with no documented rule. Valid capability names not listed.

QA topic: security-needs-declaration

## Acceptance Criteria

Doc states whether needs is enforced or advisory, lists valid capabilities


**2026-02-15 08:45 UTC:** Note: needs may still be in the grammar (grammar/directives/needs-wants.peggy) but spec v4 Section 2.3 says it's purely for package management, not security. The doc issue is presenting it as a security feature. Check if the security-needs-declaration docs topic should be removed or reclassified.
