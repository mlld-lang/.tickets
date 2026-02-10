---
id: m-7e1c
status: open
deps: []
created: 2026-02-09T08:41:30Z
type: bug
priority: 2
assignee: Adam Avenir
---
# Export system doesn't filter unexported module members

tests/cases/exceptions/export-unexported-access: Module exports { greet } but @utils._internalHelper is still accessible. Export manifest not enforced.

