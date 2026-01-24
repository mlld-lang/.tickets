---
id: mlld-sp9
status: closed
deps: [mlld-76c]
links: []
created: 2025-12-13T05:33:24.899099-08:00
type: bug
priority: 0
---
# VERIFY FIXED: Import statement tokenization complete

Import statements have multiple untokenized elements:

import templates from "@templates/agents" as @agentTemplates(message)

Missing:
1. 'templates' - the import identifier
2. '@payLoad' in other imports  
3. '@agentTemplates(message)' - the template with parameters

All three should be tokenized. Check DirectiveVisitor.visitImportDirective() to ensure all import components are visited.

## Notes

Fixed:
- Import type identifier (templates) 
- @payLoad/@state/@input sources (workaround for grammar bug mlld-qew)
- Import alias @agentTemplates
- Template parameters (message)
Need to verify all pieces highlight in editor.


