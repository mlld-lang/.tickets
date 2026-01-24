---
id: mlld-a2x
status: closed
deps: []
links: []
created: 2025-12-09T16:37:38.005938-08:00
type: task
priority: 2
---
# mlld needs simple math operators and logic

Math operators are ALREADY fully implemented in grammar and interpreter.

**Current state (VERIFIED working)**:
```mlld
/var @sum = @x + @y
/var @product = @x * @y
/var @calc = (@a + @b) * @c / @d
/when [@x + @y > 10 => show "sum exceeds 10"]
```

**Operators supported**:
- Arithmetic: `+`, `-`, `*`, `/`, `%`
- Comparison: `==`, `\!=`, `<`, `>`, `<=`, `>=`, `~=`
- Logical: `&&`, `||`, `\!`
- Ternary: `condition ? true : false`

**Grammar**: `UnifiedExpression` in `grammar/base/unified-expressions.peggy` (lines 41-49 for arithmetic)
**Interpreter**: `evaluateBinaryExpression` in `interpreter/eval/expressions.ts` (lines 151-158)

**Where they work NOW**:
1. ✅ `/var` assignments via `ExpressionWithOperator` pattern
2. ✅ `/when` conditions via `WhenConditionAdapter`
3. ✅ Array filters: `@items[?@price > 100]`
4. ❌ `/exe` blocks - NOT YET (exe blocks not implemented - see mlld-9id)
5. ❌ `let` assignments - NOT YET (let not exported from when.ts - see mlld-zeo)

**What's needed**:
NOTHING for var/when. For exe blocks and let:
- mlld-zeo exports let functions → then math works in let automatically
- mlld-9id implements exe blocks → then math works in exe returns automatically

**Close this card?** Math operators are done. The gaps are exe blocks/let implementation, not math support.

## Notes

Fixed: Math operators now work everywhere including exe blocks and let assignments.

Fix applied: Changed interpreter/core/interpreter.ts line 672-674 to use evaluateUnifiedExpression (from expressions.ts) instead of evaluateExpression (from expression.ts). The OLD evaluator only supported comparison/logical operators; the NEW evaluator supports all operators including arithmetic (+, -, *, /, %).

Test coverage added:
- tests/cases/feat/arithmetic/exe-block-basic/ (addition in exe blocks)
- tests/cases/feat/arithmetic/let-basic/ (multiplication in let)
- tests/cases/feat/arithmetic/all-operators/ (all 5 operators)

All tests pass: 2419/2419 (100%)

Refactoring issue created: mlld-xju (consolidate duplicate expression files)

Verified working:
- @a + @b in exe blocks ✅
- @x * @y in let assignments ✅
- All operators: +, -, *, /, % ✅
- Existing var/when math still works ✅


