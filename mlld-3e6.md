---
id: mlld-3e6
status: closed
deps: [mlld-jw7]
links: []
created: 2025-12-10T17:31:20.61021-08:00
type: task
priority: 1
---
# Docs: Update for block syntax, while loops, and done/continue keywords

## Summary

Update mlld documentation to reflect the new block syntax features, while loops, and done/continue control flow keywords implemented in the block syntax epic.

## Context

Epic mlld-fln introduced three major features:
1. Block syntax `[...]` for exe/for directives with let/+= support
2. While loops with done/continue keywords
3. Unified when syntax

These features are now implemented and all tests pass (Phase 2 complete, Phase 3 core tests passing). Documentation needs to be updated to reflect:
- New syntax forms
- New keywords (done, continue)
- Let/+= semantics in blocks
- Scope restrictions (what's NOT supported)

## Documentation Files to Update

Based on typical mlld docs structure, likely need updates in:

### User-Facing Docs
- `docs/flow-control.md` - Add exe blocks, for blocks, while loops
- `docs/reference.md` - Add block syntax reference
- `docs/quickstart.md` - Show simple block examples
- `docs/introduction.md` - Mention blocks if appropriate

### Developer Docs  
- `docs/dev/AST.md` - Update with new node types (ExeBlockNode, control literals)
- `docs/dev/GRAMMAR.md` - Already current, may need minor updates
- `docs/dev/DATA.md` - Verify numeric value handling documented

## What to Document

### 1. Exe Block Syntax

```mlld
/exe @greet(name) = [
  let @greeting = "Hello"
  let @punctuation = "!"
  => "@greeting @name@punctuation"
]
```

**Key points:**
- Multi-statement bodies with `[...]`
- `let @var = value` for local variables
- `let @var += value` for accumulation
- `=> value` for explicit returns (required, must be last)
- Nested for/when allowed

### 2. For Block Syntax

```mlld
/for @item in @items [
  show "Item: @item"
  let @count += 1
]
```

**Key points:**
- Multi-statement iteration bodies
- Let/+= support
- Nested control flow
- Arrow syntax deprecated but still works: `for @x in @xs => [...]`

### 3. While Loops

```mlld
/exe @countdown(n) = when [
  @n <= 0 => done "finished"
  * => continue (@n - 1)
]

/var @result = 5 | while(10) @countdown
```

**Key points:**
- Bounded iteration: `while(cap) @processor`
- Optional pacing: `while(cap, 1s) @processor`
- Control keywords: `done @value`, `continue @value`
- Context: `@ctx.while.iteration`, `@ctx.while.limit`, `@ctx.while.active`
- Retry prohibited (use continue instead)

### 4. Done/Continue Keywords

```mlld
done @value      # Terminate with value
done             # Terminate with current state
continue @value  # Next iteration with value
continue         # Next iteration with current state
```

**Key points:**
- Used in while processors
- Work in when expression actions
- Can be used in exe block returns
- Values are evaluated in current environment

### 5. Scope Restrictions (Important!)

Document what's NOT supported:
- ❌ Field access mutation: `let @data[-1].field += value` → ETOOCOMPLEX
- ❌ Parallel for with blocks: `for parallel() @x [...]` → Use exe wrapper
- ❌ Batch pipelines with blocks: `for [...] => ||` → Use simple expressions
- ❌ var +=: Only `let` supports +=, not `var`

### 6. Let vs Var Semantics

Clarify the difference:
- `let` - Block-scoped, supports +=, used in exe/for blocks
- `var` - File/module-scoped, no +=, used for state

## Validation

- [ ] All examples in docs parse correctly
- [ ] Examples match test fixtures where possible
- [ ] Restrictions clearly documented
- [ ] Migration guidance for existing patterns
- [ ] AST.md updated with new node types

## References

- Implementation: mlld-zeo, mlld-9id, mlld-0ic, mlld-ait
- Test fixtures: tests/cases/feat/exe-block, for-block, while
- Verification: tmp/phase2-implementation-verification.md, tmp/while-loop-verification.md
- Grammar docs: docs/dev/GRAMMAR.md (already current)

## Notes

Documentation updated for block syntax, while loops, and done/continue keywords.

Files updated:
- docs/user/flow-control.md: Added exe blocks, for blocks, while loops sections
- docs/user/reference.md: Added syntax references for blocks and while
- llms.txt: Added block syntax and while loops to ITERATION/EXE_EXECUTABLES sections
- docs/dev/AST.md: Added new node types (ExeBlock, For block metadata, While stages, Control literals)

All examples tested and verified working. No hallucinations - every code example runs successfully.

Test verification:
- Exe block examples ✅
- For block examples ✅  
- While loop examples ✅
- Control keywords (done/continue) ✅


