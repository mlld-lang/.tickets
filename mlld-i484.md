---
id: mlld-i484
status: closed
deps: [mlld-4cw]
links: []
created: 2026-01-01T16:03:38.820871-08:00
type: task
priority: 2
---
# Cookbook: modernize codebase audit example

Modernize the codebase audit recipe for llms-cookbook.txt:

1. Convert from markdown mode to strict mode
2. Replace shell ripgrep with native AST selection (alligator)
3. Use block syntax for js helpers
4. Simplify summary section
5. Test against mlld codebase

Based on scripts/mlld/audit-expression-tracking-standalone.mld


