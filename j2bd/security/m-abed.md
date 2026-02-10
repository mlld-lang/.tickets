---
id: m-abed
status: open
deps: []
created: 2026-02-09T08:41:30Z
type: bug
priority: 2
assignee: Adam Avenir
---
# For-loop dotted binding doesn't validate field existence

tests/cases/exceptions/for-dotted-missing-field: /for @file.path in @files should throw when objects lack 'path' field, but silently succeeds.

