---
id: m-6cf8
status: open
deps: []
created: 2026-02-17T05:00:25Z
type: feature
priority: 1
assignee: Adam
---
# Fuzzy 'did you mean @name?' suggestions for undefined variables

When validate detects an undefined variable like `@nmae`, suggest the closest match from defined variables (e.g., 'did you mean @name?'). Levenshtein distance or similar.

## Acceptance Criteria

Undefined variable warnings include fuzzy match suggestions when a close match exists

