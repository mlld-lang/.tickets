---
id: mlld-593
status: closed
deps: []
links: []
created: 2025-12-14T11:49:05.508759-08:00
type: task
priority: 0
---
# Change var/exe/guard/policy directives to use 'modifier' semantic type (pink)

Found via color testing: 'modifier' semantic token type renders as pink italic in user's theme.

Change these definition directives from 'directive' (maps to 'keyword' = light teal) to 'modifier' (pink italic):
- var
- exe  
- guard
- policy (when added)

Files to modify:
- DirectiveVisitor.ts: Update getDirectiveTokenType() or create directiveDefinition token
- TOKEN_TYPE_MAP: Add 'directiveDefinition': 'modifier'
- Remove test code after confirming colors

This gives definitions (var/exe/guard/policy) a distinct pink color from prepositions (for/when/while in light teal).


