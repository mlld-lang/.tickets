---
id: mlld-25t
status: closed
deps: []
links: []
created: 2025-12-14T07:55:36.443062-08:00
type: bug
priority: 0
---
# Fixed: Variable position bugs including export statements

Fixed offset-based position calculation for variables. Now handles:
- Bracket expressions like @var[@nested.field]  
- Export statements where location includes @
- Variables after datatype labels
- Multi-byte characters and tabs

Changes:
1. Use offset-based search (forward and backward) to find @ symbol
2. Handle 'identifier' valueType when location includes @ (export case)
3. Use document.positionAt() for accurate coordinates


