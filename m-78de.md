---
id: m-78de
status: closed
deps: []
created: 2026-02-16T00:52:02Z
type: bug
priority: 0
assignee: Adam
updated: 2026-02-16T01:49:41Z
---
# Fix cwd override to work in all block contexts

## Problem

`run cmd:@dir { ... }` sets the working directory for command execution. This works at module level and inside exe definitions, but is silently ignored inside `for` loops and `if` blocks. No error — the command just runs with the default cwd.

## What to do

Fix working directory propagation so `cmd:@dir` works inside for loops, if blocks, when blocks, and nested blocks.

### Key files

- `grammar/patterns/working-dir-path.peggy` (lines 4-12): parses the `:@dir` syntax into `WorkingDirPath`
- `grammar/patterns/unified-run-content.peggy` (line 85): stores it in `CmdCommandBrackets`
- `interpreter/eval/run-modules/run-command-executor.ts` (lines 329-334): `resolveWorkingDirectory()` reads `directive.values?.workingDir` and passes it to `commandOptions.workingDirectory`, then to `env.executeCommand()` at lines 452-458

The bug is likely that when a run directive is evaluated inside a for/if block, the `workingDir` from `directive.values` is not propagated through the block evaluation context. Check how the directive node's `values.workingDir` is preserved (or lost) when the AST is transformed or re-evaluated inside block contexts.

## Acceptance criteria

- `run cmd:@dir { command }` works inside for loops
- `run cmd:@dir { command }` works inside if blocks
- `run cmd:@dir { command }` works inside when blocks
- `run cmd:@dir { command }` works in nested blocks (for inside if, etc.)
- Add test cases for each block context in `tests/cases/`


**2026-02-16 01:49 UTC:** Added regression coverage for run cwd override across top-level, if, for, when, and nested block contexts via tests/cases/slash/run/run-cwd-in-block-contexts; full test suite passes (4202 passed, 58 skipped).
