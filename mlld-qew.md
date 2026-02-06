---
id: mlld-qew
status: closed
deps: []
links: []
created: 2025-12-13T06:38:44.36124-08:00
type: bug
priority: 2
tags: [size-s, complexity-m, risk-m, impl-none]
updated: 2026-01-31T10:19:13Z
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



**2026-01-31 10:19 UTC:** Fixed grammar to create VariableReference nodes instead of Text nodes for:
1. Special resolver imports (@payload, @state, @INPUT, @NOW, @TIME, @stdin, @keychain) - now use valueType 'specialResolver'
2. Import aliases (as @name) - now use valueType 'importAlias'

Updated:
- grammar/directives/import.peggy - 7 special resolver path handlers + 4 namespace alias handlers
- interpreter/eval/import/ImportDirectiveEvaluator.ts - support both identifier and content properties
- interpreter/eval/import/VariableImporter.ts - support both identifier and content properties
- interpreter/eval/import/ImportPathResolver.ts - detect specialResolver valueType
- services/lsp/visitors/DirectiveVisitor.ts - handle new node types for tokenization
- grammar/tests/import.test.ts - update expectations to use identifier
