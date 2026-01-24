---
id: mlld-xju
status: open
deps: []
links: []
created: 2025-12-10T18:32:55.307628-08:00
type: task
priority: 2
---
# Refactor: Consolidate expression.ts and expressions.ts

## Summary

Consolidate duplicate expression evaluator files to eliminate confusion and technical debt.

## Context

We have TWO files with overlapping functionality:
1. `interpreter/eval/expression.ts` (OLD) - 347 lines
2. `interpreter/eval/expressions.ts` (NEW) - ~350 lines

The OLD file contains useful helpers (isTruthy, isEqual, toNumber) but also a broken `evaluateExpression()` that doesn't support arithmetic operators. The NEW file has the working evaluator but imports helpers from the OLD file.

**Current fix:** interpreter.ts now uses the NEW evaluator (mlld-a2x fix), but we should consolidate the files.

## Current Usage

**expression.ts (OLD):**
- Exports: isTruthy, isEqual, toNumber, evaluateExpression
- Used by: expressions.ts (helpers), value-combine.ts (toNumber)
- evaluateExpression: UNUSED (was broken, replaced by evaluateUnifiedExpression)

**expressions.ts (NEW):**
- Exports: evaluateUnifiedExpression
- Imports: isTruthy, isEqual, toNumber from expression.ts
- Used by: interpreter.ts (as of mlld-a2x fix)

## Consolidation Options

### Option 1: Merge into expressions.ts (Recommended)

**Steps:**
1. Copy isTruthy, isEqual, toNumber into expressions.ts
2. Remove import from expressions.ts
3. Export helpers from expressions.ts
4. Update value-combine.ts to import from expressions.ts
5. Delete expression.ts entirely

**Result:** Single file with all expression logic

**Pros:** Clean, single source of truth
**Cons:** Larger file (~650 lines)

### Option 2: Rename to expression-helpers.ts

**Steps:**
1. Rename expression.ts → expression-helpers.ts
2. Remove evaluateExpression() from helpers file (keep only isTruthy, isEqual, toNumber)
3. Update imports in expressions.ts and value-combine.ts

**Result:** Clear separation (helpers vs evaluators)

**Pros:** Explicit naming, smaller files
**Cons:** Extra file

## Files to Modify

Option 1 (Merge):
- interpreter/eval/expressions.ts (add helpers)
- interpreter/utils/value-combine.ts (update import)
- Delete interpreter/eval/expression.ts

Option 2 (Rename):
- Rename interpreter/eval/expression.ts → expression-helpers.ts
- Update interpreter/eval/expressions.ts import
- Update interpreter/utils/value-combine.ts import
- Remove evaluateExpression() from helpers

## Validation

- [ ] All tests pass after consolidation
- [ ] No imports from expression.ts remain
- [ ] Helpers (isTruthy, isEqual, toNumber) still work
- [ ] evaluateUnifiedExpression still works
- [ ] value-combine.ts still works

## Effort

**Option 1 (Merge):** 30-45 minutes
**Option 2 (Rename):** 20-30 minutes

## References

- Fixed in: mlld-a2x (interpreter.ts now uses NEW evaluator)
- Analysis: tmp/expression-consolidation-plan.md


