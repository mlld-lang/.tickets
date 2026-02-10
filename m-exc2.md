---
id: m-exc2
status: closed
deps: []
created: 2026-02-09T14:45:15Z
type: task
priority: 1
assignee: Adam Avenir
tags: [urgency-high, excellence]
updated: 2026-02-09T14:53:26Z
---
# Fix excellence gaps: cross-references and pattern discoverability

Excellence assessment found discovery and consistency gaps. Fix these small-effort items:

1. Add pattern-dual-audit to pattern-audit-guard.md related field, and add a note at the bottom pointing to the hardened version
2. Add pattern-audit-guard and pattern-dual-audit to signing-overview.md related field
3. Add pattern-audit-guard and pattern-dual-audit to labels-influenced.md related field
4. In pattern-dual-audit atom, acknowledge that user-defined privileged guards are not yet available and the bless-on-verdict step currently requires policy-generated guards
5. Align enforcement guard action: pattern-dual-audit uses 'deny' but pattern-audit-guard uses 'retry'. Use 'retry' consistently.
6. In main.mld, add a clearly-labeled production configuration comment block showing exe llm declarations with @-referenced signed templates

