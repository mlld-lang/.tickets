---
id: mlld-fln
status: closed
deps: []
links: []
created: 2025-12-09T22:13:32.943942-08:00
type: feature
priority: 1
parent: mlld-cw9
---
# Epic: Block Syntax, While Loops, and Grammar Error Handling

A unified grammar epic that adds three major features to mlld:

1. **Block syntax `[...]`** for exe/for directives with `let`/`+=` support
2. **While loops** with `done`/`continue` keywords for bounded iteration
3. **Unified when syntax** - one form: `when [condition => action]`
4. **Improved error handling** - proper location tracking in blocks

## Key Insight

All three features use `[...]` delimiter for mlld constructs, creating clear visual separation from JS/shell `{...}` syntax. This helps LLMs distinguish mlld from embedded languages.

## Why Together

Same files, same patterns, shared error infrastructure. More efficient as one epic than three separate PRs.

## Block Syntax (CRITICAL)

**For loops**:
- Preferred: `for @x in @xs [...]` (no arrow)
- Deprecated: `for @x in @xs => [...]` (arrow still works but deprecated)

**Exe blocks**:
- `exe @f() = [...]`

**When**:
- Block: `when [...]`
- Simple: `when @condition => action`

## Delimiter Rules

- `{...}` → code/command/data (requires prefix or property syntax)
- `[...]` → mlld blocks in exe/for/when contexts
- `[...]` → array literals in var/argument contexts
- **Context determines meaning** - No ambiguity!

## New Keywords

- `done` and `continue` (for while loops)
- `let` already exists for when blocks

## Scope Restrictions (Pragmatic)

**NOT Implementing:**
- Field access mutation: `let @data[-1].field += value` → ETOOCOMPLEX
- Parallel for with blocks: `for parallel() @x [...]` → Use exe wrapper
- Batch pipelines with blocks: `for [...] => ||` → Use simple expressions
- When match form: `when @var: [...]` → Use `when [@var == value => ...]`
- **var +=**: No augmented assignment for var; use `let` in blocks

**ARE Implementing:**
- Block syntax `[...]` for multi-statement bodies (exe, for)
- Simple let/+=: `let @var = value` and `let @var += value` (variable name only, no field access)
- Nested control flow: for/when inside blocks
- While loops: Bounded iteration with done/continue
- Explicit returns: `=> @value` (required in exe blocks, must be last)
- Proper error handling: Location tracking, helpful messages
- Strict/loose mode: No slashes in strict, top-level only in loose

## Implementation Phases

1. **Phase 0**: Abstraction Discovery (60-90 min reading before code) ✅
2. **Phase 1**: Grammar + Types (all features, parse-only focus) ✅
3. **Phase 2**: Interpreter (one construct at a time) - IN PROGRESS
4. **Phase 3**: Core Tests (minimal golden set - 5 tests)
5. **Phase 4**: Integration & Polish (optional)

## Success Criteria

1. All core test cases pass
2. Exe blocks can have sequential statements with let/+=
3. For blocks can have nested control flow
4. Let variables properly scoped to blocks
5. Augmented assignment works on arrays, strings, objects
6. Return statements work in exe blocks
7. Error messages clear and actionable
8. Nested blocks maintain separate scopes

## Estimate

3-5 Claude sessions (grammar-first approach, abstraction reuse focus)

## Reference

See `todo/plan-grammar-next.md` for full implementation details.

## Notes

Epic complete! All features implemented, tested, and documented.

Completed:
✅ Block syntax for exe/for with [...]
✅ While loops with done/continue
✅ Let/+= in blocks
✅ All tests passing (2419 tests)
✅ Documentation updated (flow-control.md, reference.md, llms.txt, AST.md)
✅ Bonus: Math operators fixed (mlld-a2x)

All phases complete:
- Phase 0: Abstraction Discovery ✅
- Phase 1: Grammar + Types (6 tasks) ✅
- Phase 2: Interpreter (4 tasks) ✅
- Phase 3: Core Tests ✅
- Phase 4: Integration Tests ✅

Ready for production!


