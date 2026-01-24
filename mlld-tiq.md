---
id: mlld-tiq
status: closed
deps: []
links: []
created: 2025-12-18T11:50:32.738424-08:00
type: bug
priority: 1
---
# Directory imports: exported variables missing, only __meta__ (frontmatter) visible

When using directory imports, child modules' exported variables are not accessible. Only the frontmatter __meta__ property is visible.

Reproduction:
- File agents/party/index.mld exports @meta, @tldr
- /import "./agents" as @registry
- @registry.party only shows __meta__ (frontmatter), not the exported variables

The issue appears to be in ModuleContentProcessor.ts where files without populated moduleObjects fallback to template mode even though they have valid exports.


