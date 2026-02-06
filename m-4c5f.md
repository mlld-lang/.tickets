---
id: m-4c5f
status: open
deps: []
created: 2026-01-29T19:04:06Z
type: task
priority: 1
assignee: Adam
tags: [scope-testing, risk-m, size-l, needs-human-design]
updated: 2026-01-30T22:45:10Z
---
# Design testing conventions for complex mlld workflows

The polish pipeline bug (m-7cd0) wasn't caught by unit tests because isolated tests passed while integrated pipeline failed. Need testing patterns that catch integration issues in complex multi-module workflows.

Considerations:
- mlld test exists but is dated
- Polish workflow has multiple phases, imports, parallel execution
- Pattern should be reusable for other complex workflows

## Acceptance Criteria

- Document testing conventions for multi-module workflows
- Evaluate/update mlld test infrastructure
- Add integration smoke test for polish pipeline
- Pattern documented for others to follow

