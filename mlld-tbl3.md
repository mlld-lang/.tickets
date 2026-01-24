---
id: mlld-tbl3
status: closed
deps: []
links: []
created: 2025-12-20T15:21:44.566203-08:00
type: bug
priority: 1
---
# Directory import converts hyphens to underscores in registry keys

When importing a directory as a registry (e.g. `import "@agents" as @agentRegistry`), files with hyphens in their names get converted to underscores in the registry keys.

Example:
- File: `mlld-devrel/index.mld`  
- Expected key: `mlld-devrel`
- Actual key: `mlld_devrel`

This breaks lookups when agent IDs from other sources (like frontmatter) use the original hyphenated names.

@adam confirmed: preserve hyphens in keys - there's no reason not to.


