---
id: mlld-7t6d
status: open
deps: []
links: []
created: 2026-01-04T19:24:52.320764-08:00
type: feature
priority: 3
---
# Git hook to surface potentially stale docs after commits

Create a post-commit hook that intelligently identifies docs that may need updating based on code changes. Could compare changed files against related-code/related-docs frontmatter in docs/dev/, or use heuristics like 'interpreter changes might affect directive docs'. Informational only - prints suggestions, doesn't block.


