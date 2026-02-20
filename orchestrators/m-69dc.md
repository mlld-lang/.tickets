---
id: m-69dc
status: open
deps: [m-5870, m-3f0b, m-fd36, m-a3fe, m-9bab, m-5c21]
created: 2026-02-20T01:13:20Z
type: task
priority: 3
assignee: Adam Avenir
updated: 2026-02-20T01:13:26Z
---
# Update skills and examples in plugins/ to match orchestrator conventions

## Context

After implementing the llm/ improvements (shared libraries, output conventions, config patterns, path aliases), the skills and examples in plugins/ should be updated to reflect the new conventions.

## Work

1. Review all skills in plugins/ that reference orchestrator patterns
2. Update examples to use @lib/ imports instead of local duplicates
3. Update examples to use @output/ for run output
4. Update scaffold/example skills to follow conventions from llm/CONVENTIONS.md
5. Ensure any orchestrator-related skills demonstrate the config-driven pattern where applicable

## Dependency

This should be done AFTER the shared library extraction, output migration, and review generalization are complete.

