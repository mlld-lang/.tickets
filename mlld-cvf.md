---
id: mlld-cvf
status: closed
deps: [mlld-76c]
links: []
created: 2025-12-13T05:31:33.985242-08:00
type: bug
priority: 1
---
# Arrow operator => needs distinct highlighting from = && as from

The => operator should stand out visually from other operators and keywords:
- = (assignment)
- && (logical and)
- as, from (keywords)

Currently they all look similar. => is the most important flow control operator and should be highly visible.

## Notes

✅ COMPLETE - All => operators now use 'modifier' semantic type (pink like var/exe)

**Final fix count: 12 locations across 2 files**

DirectiveVisitor.ts (9 locations):
1. Line 2481 - /for inline syntax (=> action)
2. Line 2568 - /exe block return statement (=> expression)  
3. Line 2792 - /guard rules (condition => action)
4. Line 1460 - /when directive single condition (=> action)
5. Line 1761 - /when directive pairs with arrowLocation
6. Line 1778 - /when directive pairs fallback
7. Line 1897 - /when directive condition => action
8. Line 1859 - /when blocks after ] => action
9. Line 1922 - /when => operator (was never tokenized before - now added!)

ExpressionVisitor.ts (3 locations):
10. Line 400 - ForExpression inline (for @x in @xs => @x)
11. Line 234 - WhenExpression condition => action (primary)
12. Line 241 - WhenExpression fallback (condition => action)

**Result:**
ALL => operators now render in pink italic (same as var/exe directives), including:
- exe blocks with return statements
- exe when expressions (exe @f() = when first [ @x > 0 => @y ])
- for loops (inline and directive forms)
- when expressions and directives
- guard rules

Making flow control transitions highly visible everywhere!


