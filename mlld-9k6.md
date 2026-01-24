---
id: mlld-9k6
status: closed
deps: []
links: []
created: 2025-12-14T12:27:03.205854-08:00
type: bug
priority: 0
---
# Fix function call arguments not highlighting

Arguments in function invocation position not highlighting:

Example: @agentTemplates[@responder.agent](@messageBody)
The @messageBody in argument position is not highlighted.

Also: @haiku(@prompt) - @prompt not highlighted

Issue: ExecInvocation arguments need tokenization. Currently only the function name and operators are tokenized, but the arguments (VariableReference nodes in args array) are not being visited.

Files: CommandVisitor.ts - check ExecInvocation handling


