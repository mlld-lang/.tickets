---
id: mlld-d49g
status: closed
deps: []
links: []
created: 2026-01-11T16:26:21.751489-08:00
type: task
priority: 0
---
# CodeFence nodes emitted during module import

When importing literate .mld.md modules (like @mlld/claude), CodeFence nodes (documentation code blocks marked with ```mlld) are unconditionally emitted to stdout. Only MlldRunBlock nodes (```mlld-run) should produce output during import.

**Root cause**: interpreter/core/interpreter.ts lines 287-298 - CodeFence nodes call env.emitIntent() with visibility: 'always' regardless of context.

**Fix**: Check if context?.isExpression is true (import context) and skip CodeFence emission.

**Files**:
- interpreter/core/interpreter.ts (lines 287-298, 379-390)
- interpreter/eval/import/ModuleContentProcessor.ts (passes isExpression: true at line 955)

**Reproduction**: Run any script that imports @mlld/claude - documentation examples appear in output.


