---
id: mlld-ahug
status: closed
deps: []
links: []
created: 2025-12-20T19:42:23.819681-08:00
type: bug
priority: 1
---
# Fixed: cmd inside exe...when first returns empty

Bug: `cmd` inside `exe ... = when first [...]` returns empty.

Root cause: The grammar was emitting `type: 'UnifiedVariableReferenceWithTail'` for variable references with pipelines in when-expression actions, but this type wasn't handled by the main `evaluate()` function. When the unknown type was encountered, an error was thrown and silently caught by the when-expression evaluator, causing it to fall through to the default case.

Fix:
1. Changed grammar to emit `type: 'VariableReferenceWithTail'` (consistent naming)
2. Added proper interface `VariableReferenceWithTailNode` in core/types/primitives.ts
3. Added handler for `VariableReferenceWithTail` in interpreter/core/interpreter.ts

Reported by @partydev in mm chat.


