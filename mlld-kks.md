---
id: mlld-kks
status: closed
deps: []
links: []
created: 2025-12-12T18:08:33.943636-08:00
type: bug
priority: 0
---
# False error on valid import with 'as' clause

Line marked as error: /import templates from "@templates/agents" as @agentTemplates(message). This is valid syntax but LSP reports it as invalid. Red squiggly appearing on valid import directive. File: orchestrate.mld line 18

## Notes

Fixed by updating checkStrictModeTextNodes to only check top-level Text nodes


