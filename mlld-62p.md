---
id: mlld-62p
status: open
deps: []
links: []
created: 2025-12-09T12:26:52.590565-08:00
type: task
priority: 2
---
# Audit: Ensure universal StructuredValue compliance

## Goal

Audit and fix the codebase to ensure **universal StructuredValue compliance**:

**All runtime values are StructuredValues** - primitives, strings, arrays, objects, loaded content.

## Policy (now in DATA.md)

**Universal StructuredValue flow:**
1. **Grammar**: Returns AST Literal nodes `{type: 'Literal', value: 42}`
2. **Interpreter**: Wraps ALL values in StructuredValue `{type: 'number', text: '42', data: 42, ctx: {...}}`
3. **Variables**: Wrap StructuredValues (StructuredValueVariable)
4. **Boundaries**: Use `asData()` for computation, `asText()` for display

## Rationale

We tried making primitives exceptions but the system fights that. The evidence:
- `wrapExecResult`, `ensureStructuredValue`, pipelines already wrap primitives
- Dual representation IS useful: `.text` for templates, `.data` for comparisons, `.ctx` for guards
- Consistency wins: one model is simpler than special cases
- We were adding unwrapping hacks everywhere - fighting the design

## Audit Strategy

### Find places that DON'T use asData()/asText()

**Computation boundaries that need `asData()`:**
- ✅ Comparisons (`toNumber`, `isEqual`) - FIXED
- Array methods (`.includes()`, `.indexOf()`, `.join()`, etc.)
- String methods (`.startsWith()`, `.substring()`, etc.)
- Arithmetic operations
- Boolean operations
- Object property access
- Array indexing

**Display boundaries that need `asText()`:**
- Template interpolation
- Shell command arguments  
- `/show` output
- Log messages
- JSON.stringify contexts

### Find places trying to unwrap primitives

Search for patterns like:
- Checking `typeof value === 'number'` without extracting first
- Creating PrimitiveVariable instead of StructuredValueVariable for exec results
- Avoiding wrapping in `ensureStructuredValue`

### Implementation

Use mlld to parallelize:
1. Spawn haiku agents for each file category
2. Each outputs JSON: `{file, needsAsData: [...], needsAsText: [...], wrongUnwrap: [...]}`
3. Aggregate to `audit-results.jsonl`
4. Generate fix plan
5. Apply fixes (parallelized if many)

## Current Status

**Fixed:**
- ✅ `toNumber()` extracts `.data` from StructuredValue
- ✅ `extractValue()` in expression.ts handles StructuredValue
- ✅ Grammar returns Literal nodes consistently
- ✅ PrimitiveEvaluator extracts Literal values
- ✅ Object property evaluation handles Literal nodes

**Likely still broken:**
- ❓ Array/string method arg processing
- ❓ Arithmetic expression evaluation
- ❓ Direct property access on StructuredValues

## Next Steps

1. Run the audit script to find all missing `asData()`/`asText()` calls
2. Prioritize by user impact (comparison operators were P1)
3. Fix systematically


