---
id: m-8bc2
status: open
deps: []
created: 2026-02-19T17:14:52Z
type: bug
priority: 0
assignee: Adam Avenir
external-ref: gh-550
---
# Grammar: inline >> comments after ] fail to parse in strict mode

Inline >> comments after a closing bracket ] on the same line fail to parse in strict mode (.mld files). Example: ] >> end phase 1 gives 'Text content not allowed in strict mode'. The >> should start a comment regardless of position on the line. Workaround: put comments on separate lines.

## Acceptance Criteria

1. >> after ] on the same line is parsed as a comment in strict mode. 2. No regressions in existing comment parsing.

