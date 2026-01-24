---
id: mlld-4xm
status: closed
deps: []
links: []
created: 2025-12-09T11:59:53.332409-08:00
type: bug
priority: 1
---
# When expressions returning numbers produce empty output

## Summary

User partydev.3 reported that `/exe` with `when first` returning numeric values produces empty output instead of the number.

## Root Cause

Grammar was inconsistent about primitive handling:
- Some patterns returned raw primitives (`42`)
- Some returned Literal AST nodes (`{type: 'Literal', value: 42}`)
- Interpreter had gaps in Literal node handling

This violated the fundamental principle: **Grammar should always return AST nodes, interpreter extracts values.**

## Policy Decision (Documented in DATA.md)

**Primitives are NEVER StructuredValues:**
1. Grammar returns Literal AST nodes: `{type: 'Literal', value: 42}`
2. Interpreter extracts native values: `42`
3. Variables wrap primitives: `PrimitiveVariable{value: 42, ctx: {...}}`
4. StructuredValue is ONLY for dual representations (loaded content, pipeline results)

**Rationale:**
- Primitives have no dual representation (`42` is just `42`)
- No provenance for literals in code
- Variables already provide metadata layer
- Performance - no wasteful wrapping

## Fixes Applied

1. **Grammar (`var-rhs.peggy`)**: `PrimitiveValue` now wraps all primitives in Literal nodes
2. **When evaluator (`when-expression.ts`)**: `normalizeActionValue()` extracts values from Literal nodes
3. **Data evaluator (`PrimitiveEvaluator.ts`)**: Added Literal node handling
4. **Object processing (`var.ts`)**: `evaluateArrayItem()` extracts values from Literal nodes

## Testing

- ✅ All 2367 tests pass
- ✅ Numeric returns in when expressions work
- ✅ Object serialization preserves types: `{"count":42}` not `{"count":"42"}`
- ✅ Array methods with numeric args work: `@arr.includes(3)`

## Follow-up

Created mlld-62p for full codebase audit to ensure all primitive handling is consistent.


