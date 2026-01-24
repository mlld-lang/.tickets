---
id: mlld-8sn
status: closed
deps: [mlld-76c]
links: []
created: 2025-12-13T05:32:26.559376-08:00
type: bug
priority: 0
---
# Parameters not highlighting in editor despite validator passing

VERIFIED FIXED: Parameters now gold colored.

Final solution:
- Keep parameters as 'parameter' semantic type
- Remove highlight group override (let theme's natural parameter color apply)
- Theme's natural parameter color = gold ✓
- Fixed argument visiting bug in CommandVisitor.ts for computed property calls

Parameters now work everywhere:
- exe declarations: @func(agent, msg) → gold ✓
- Import templates: @agentTemplates(message) → gold ✓
- Function call args: @haiku(@prompt) → highlighted ✓
- Computed calls: @templates[@key](@arg) → highlighted ✓

## Notes

Validator investigation complete:
- Parameter tokens ARE being generated correctly (validator shows 100% coverage)
- Positions are correct: agent@19, msg@26, turnP@31 on line 8
- FileReferenceVisitor.visitParameter() is correctly registered and implemented
- TOKEN_TYPES includes 'parameter' at index 6
- Same ASTSemanticVisitor used by both validator and LSP server

Issue must be in:
1. LSP protocol encoding/transmission
2. Editor not receiving tokens
3. Different code path in actual LSP server
4. Or editor configuration issue

Next: Test actual LSP server output to editor


