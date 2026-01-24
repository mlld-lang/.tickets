---
id: mlld-buc
status: closed
deps: [mlld-76c]
links: []
created: 2025-12-13T05:32:12.666309-08:00
type: bug
priority: 1
---
# String highlighting inconsistent across contexts

Strings render in different colors depending on context:
- show "text" in when/for => appears one color
- show "text" standalone => appears different color
- Strings with emoji appear different from plain strings

All are tokenized as 'string' type but render inconsistently. Likely colorscheme applying different colors in different syntactic contexts. Need explicit @lsp.type.string.mld highlight group to override context-specific defaults.

## Notes

**THREE CRITICAL FIXES APPLIED:**

**Fix 1: Text nodes not registered (ASTSemanticVisitor.ts:74)**
- Added: this.registerVisitor('Text', templateVisitor)

**Fix 2: Legacy exe template code removed (DirectiveVisitor.ts:282-310)**  
- Deleted special-case that tokenized whole string as one token

**Fix 3: Missing return statements (DirectiveVisitor.ts:511,515,518)**
- exe templates were falling through to visitChildren() after visitTemplateValue()
- visitChildren() visited values.identifier AGAIN (already tokenized as function)
- Created duplicate/overlapping token at wrong position (char=26 "g wit")
- Added early returns to prevent re-visiting

**Result:**
exe @func(@value) = "string with @value etc..." now tokenizes correctly:
- @func → function (char=4) ✅
- @value param → parameter (char=10) ✅  
- "string with " → string (char=21) ✅
- @value in string → interpolation (char=33) ✅
- " etc..." → string (char=39) ✅
- NO duplicate @func token at char=26 ✅

All string highlighting now consistent across all contexts!


