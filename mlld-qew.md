---
id: mlld-qew
status: open
deps: []
links: []
created: 2025-12-13T06:38:44.36124-08:00
type: bug
priority: 2
---
# Grammar bug: Special resolver imports parsed as Text not VariableReference

Multiple grammar bugs where variables are parsed as Text nodes instead of VariableReference:

1. Special resolver imports (@payLoad, @state, @input, @now, @time):
   Location: grammar/directives/import.peggy:411-502
   Creates: helpers.createNode(NodeType.Text, { content: matched })
   Should be: VariableReference with proper valueType

2. Import alias names (@agentTemplates):
   The alias in 'as @agentTemplates' is a Text node with content='agentTemplates' (no @)
   Should be: VariableReference or at minimum include @ in content

Impact:
- LSP can't tokenize these properly without workarounds
- AST semantics incorrect (they're variable references, not text)
- Fixed with workarounds in DirectiveVisitor but should fix in grammar for correctness

Workarounds added:
- DirectiveVisitor:2080-2089 - Detects Text nodes starting with @ in import paths
- DirectiveVisitor:2206-2250 - Tokenizes import alias and parameters from raw data


