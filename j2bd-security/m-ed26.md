---
id: m-ed26
status: closed
deps: []
created: 2026-02-09T18:40:34Z
type: task
priority: 2
assignee: Adam Avenir
tags: [excellence, polish]
updated: 2026-02-09T18:45:18Z
---
# Polish: PG-4/5/6 minor doc fixes for policy atoms

Address three minor polish gaps from excellence assessment:

PG-4: Add qa_tier: 2 to frontmatter of policy-composition.md and policy-label-flow.md (for consistency with other atoms like labels-sensitivity.md and policies.md).

PG-5: In policy-composition.md, swap the order of the first two code examples. Put the self-contained inline two-policy example FIRST (currently lines 31-46), then the import pattern SECOND (currently lines 15-20). A new user reading top-to-bottom should see a complete working example before the import shorthand.

PG-6: In policy-composition.md, after the composition rules table (after line 30), add a one-line note: 'Note: If allow lists have no overlap, the intersection is empty and all operations are blocked. Ensure shared baseline commands appear in all layers.'

Validate with mlld validate after changes.

