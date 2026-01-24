---
id: mlld-514
status: closed
deps: []
links: []
created: 2025-12-12T18:09:31.610153-08:00
type: bug
priority: 1
---
# Bracket property access causes undefined token length

Complex expressions like @agentTemplates[@responder.agent](@messageBody) produce tokens with undefined length. Partially mitigated with safety checks but underlying calculation bug remains. Line 54 of orchestrate.mld

## Notes

Fixed bracket property access: added variableIndex handling in OperatorTokenHelper and VariableVisitor to visit nested VariableReferences


