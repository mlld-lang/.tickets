---
id: mlld-cyz
status: closed
deps: [mlld-76c]
links: []
created: 2025-12-13T05:33:40.413955-08:00
type: bug
priority: 2
---
# Comment highlighting inconsistent - first comment after stanza not highlighted

Comment highlighting is inconsistent. First >> comment line after another stanza (exe, var, etc) doesn't highlight as comment, but subsequent >> lines do.

Examples in orchestrate.mld:
- Line with '>> Evaluate a single agent...' after exe block → not highlighted
- Subsequent >> comments → highlighted correctly

Likely a grammar issue - parser may be in wrong state after closing stanza, treating first >> as something else. Check Comment node generation in grammar.


