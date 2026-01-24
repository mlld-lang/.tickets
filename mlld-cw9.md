---
id: mlld-cw9
status: closed
deps: []
links: []
created: 2025-12-09T22:14:05.424356-08:00
type: task
priority: 0
---
# Phase 0: Abstraction Discovery for Block Syntax Epic

**CRITICAL: This must be done FIRST before any implementation!**

## Purpose

Before writing any new grammar, systematically identify what already exists. The plan was created with "late-context chaotic planning energy" - a fresh implementer's brain will see patterns we missed.

## Time Budget

60-90 minutes of reading before writing code.

## Required Reading

### 1. Grammar Files to Review

**`grammar/patterns/exe-rhs.peggy`**
- What can `WhenExpression` currently contain? (lines 438-517)
- What's in `WhenExpressionEntry`? (line 502-505)
- What's `WhenRHSAction`? (references when-rhs-action.peggy)
- Can we reuse `WhenExpressionConditionList` for exe blocks?

**`grammar/patterns/when-rhs-action.peggy`**
- What actions are supported? (line 8-17)
- What directives can appear in when RHS?
- Which of these should work in exe blocks?

**`grammar/patterns/iteration.peggy`**
- What's in `ForSingleAction`? (lines 55-101)
- Does it already support everything for blocks need?
- What's `WhenRHSAction` reused here?

**`grammar/base/literals.peggy`**
- How are `retry`, `skip`, `allow`, `denied` defined? (lines 50-74)
- Follow exact same pattern for `done` and `continue`

**`grammar/patterns/let-assignment.peggy`**
- How do `LetAssignment` and `AugmentedAssignment` work?
- Can we reuse directly or need exe-specific variants?

### 2. Type Files to Review

**`core/types/` for exe/for/when**
- Understand `ExecutableDefinition` variants
- How does dispatch work?
- Where does `ExeBlockNode` fit in hierarchy?

### 3. Data Handling (CRITICAL!)

**`docs/dev/DATA.md`** - Required reading!
- StructuredValue system (.text, .data, .ctx)
- When to use asData() vs asText()
- Boundary rules for unwrapping

### 4. Files to AVOID

**`grammar/directives/for-template.peggy`** - NOT reusable (text substitution)

## Key Insight from Plan

Exe blocks are ~80% the same as exe..when! An exe block is just when expression without conditions (or with implicit `* =>`). Massive reuse opportunity.

## Critical Disambiguation (SOLVED in plan)

In exe/for contexts, `[...]` is **always a block**:
```mlld
exe @func() = [1, 2, 3]           # Block with 3 expressions (likely error)
exe @func() = {[1, 2, 3]}         # Escape hatch: object with array
exe @func() = @identity([1, 2, 3]) # Or use function

for @x in @xs => [statements]     # Always block
```

**No lookahead needed** - Context determines meaning.

## Deliverable

Create `tmp/abstraction-reuse-findings.md` documenting:
1. What patterns exist and can be reused (with line numbers)
2. What needs creating (minimal list)
3. Gap analysis: exe..when vs exe blocks
4. Solution for array literal disambiguation
5. Type hierarchy understanding
6. StructuredValue usage patterns

## Validation

- [ ] Read all listed files
- [ ] Created findings document
- [ ] Identified reuse opportunities
- [ ] Ready to start Phase 1

## Warning

The plan shows test examples with BOTH `{...}` and `[...]` syntax due to iteration during planning. **The final decision is `[...]` for all mlld blocks**. When implementing, convert all block examples to use `[...]` brackets, not `{...}` curly braces.

## Notes

Phase 0 reading done; documented reuse/gaps in tmp/abstraction-reuse-findings.md; ready to proceed to block grammar/AST work.


