---
id: m-dbae
status: open
deps: []
created: 2026-02-25T19:15:35Z
type: chore
priority: 3
assignee: Adam Avenir
---
# Improve CLI test coverage

CLIOrchestrator has no dedicated tests. cli-integration.test.ts mostly checks command metadata shape, not execution behavior. Add smoke tests for CLIOrchestrator main flow and improve integration test coverage for actual command execution.

