---
id: mlld-7t7
status: closed
deps: []
links: []
created: 2025-12-12T18:07:56.013209-08:00
type: bug
priority: 0
---
# @variables appear as plain text in LSP highlighting

Variable highlighting issues in LSP:

1. Method highlighting broken - counting is off, parens inconsistent (e.g., .indexOf(@agent) has wrong positions)
2. @ symbol should be colored as part of variable name, not separate. Methods (.trim(), .startsWith()) should match variable color
3. Special variables (@ctx, @state, @time, @input) need distinct highlighting from regular variables

File: orchestrate.mld - multiple lines affected

## Notes

Fixed variable highlighting: combined @ with name, added field tokenization for {{var.field}}, achieved 100% token coverage


