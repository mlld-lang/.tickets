---
id: mlld-70b
status: closed
deps: []
links: []
created: 2025-12-14T11:25:57.343106-08:00
type: task
priority: 1
---
# Change security labels to parameter token type

Change security labels (like 'label' in 'var label @x') from 'label' → 'parameter' for light orange/beige color.

Files to modify:
- DirectiveVisitor.ts tokenizeSecurityLabels() - change tokenType from 'label' to 'parameter'
- Update highlight group in nvim config (already has @lsp.type.parameter.mld)


