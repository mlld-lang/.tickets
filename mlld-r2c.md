---
id: mlld-r2c
status: closed
deps: []
links: []
created: 2025-12-14T11:25:55.135558-08:00
type: task
priority: 1
---
# Change run/show/output/append/log/stream to property + italic (darker teal)

Change action directives to use 'property' semantic type with italic modifier for darker teal italic appearance (like property_example in .ts).

Token type: 'directiveAction' → maps to 'property' in TOKEN_TYPE_MAP
Add italic modifier when tokenizing

Currently: 'directive' → 'keyword' (light teal italic)
Target: 'property' + italic (darker teal italic)

Files: DirectiveVisitor.ts, TOKEN_TYPE_MAP


