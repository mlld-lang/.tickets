---
id: mlld-0ic
status: closed
deps: [mlld-0gd, mlld-zeo]
links: []
created: 2025-12-09T22:18:12.164593-08:00
type: task
priority: 1
---
# Phase 2.3: Interpreter - Modify for.ts for block action support

## Summary

Modify the for loop evaluator to detect block vs single action mode and evaluate blocks with let support.

## Key Insight

ForSingleAction already supports everything for blocks need. The change is minimal - detect action type and call the right evaluator.

## 📚 Required Reading

Before starting this task:
- **docs/dev/DATA.md** - StructuredValue system (.text, .data, .ctx)
- **interpreter/utils/structured-value.ts** - asData/asText helpers

Key patterns:
- All values flow as StructuredValue with `.text`, `.data`, `.ctx`
- `asData()` for computation boundaries (JS args, comparisons)
- `asText()` for display boundaries (templates, shell commands)
- Let assignments store StructuredValue wrappers
- Iterator values preserve wrappers through loop body

## ❌ DON'T DO (Explicit Restrictions)

These combinations are **NOT supported** in this epic:

1. **Parallel for with blocks**: `for parallel() @x in @xs [...]]`
   - Error: "Parallel for loops not supported with block bodies. Use exe wrapper pattern."
   - Workaround: Create an exe that contains the block logic, use parallel for with that exe.

2. **Batch pipelines with blocks**: `for @x in @xs => [...] => || @batch()`
   - Error: "Batch pipelines not supported with block bodies. Use simple for-expression."
   - Workaround: Use simple for-expression if batch pipeline needed.

3. **Field access mutation in +=**: `let @data[-1].field += value`
   - Error: "ETOOCOMPLEX: Augmented assignment only supports simple variables."
   - Only support: `let @variable += value` (simple variable names)

4. **var +=**: There is no `var +=` syntax. Use `let` for accumulation within blocks.

## Prereq

- mlld-0gd (Phase 1 verification)
- mlld-zeo (Export let functions)

## Files to Modify

- `interpreter/eval/for.ts` - Modify runOne() function

## Implementation

Update runOne() function (around line 168):

```typescript
import { isLetAssignment, isAugmentedAssignment } from '@core/types/when';
import { evaluateLetAssignment, evaluateAugmentedAssignment } from './when';

const runOne = async (entry: [any, any], idx: number) => {
  // ... existing setup code for childEnv, iterationVar, etc.

  const actionNodes = directive.values.action;
  const retry = new RateLimitRetry();

  while (true) {
    try {
      // Handle block vs single action mode
      if (directive.meta.actionType === 'block') {
        // Block mode: sequential evaluation with let support
        let blockEnv = childEnv;
        for (const stmt of actionNodes) {
          if (isLetAssignment(stmt)) {
            blockEnv = await evaluateLetAssignment(stmt, blockEnv);
          } else if (isAugmentedAssignment(stmt)) {
            blockEnv = await evaluateAugmentedAssignment(stmt, blockEnv);
          } else {
            const result = await evaluate(stmt, blockEnv);
            blockEnv = result.env || blockEnv;
          }
        }
        childEnv = blockEnv;
      } else {
        // Single action mode (existing behavior - unchanged)
        // ... existing code
      }

      retry.reset();
      break;
    } catch (err: any) {
      // ... existing retry logic unchanged
    }
  }
  return;
};
```

## Scoping Rules

- Each iteration gets fresh child environment
- Let variables scoped to block (not visible across iterations)
- `let` in outer exe block CAN be mutated from inner for block (lexical scoping)
- Parallel execution unchanged (blocks run sequentially within each parallel iteration)

## Validation

- [ ] Block mode detected via meta.actionType
- [ ] Let assignments create scoped variables per iteration (as StructuredValue)
- [ ] Augmented assignments work within iteration
- [ ] Parallel execution still works for single actions
- [ ] Error messages for unsupported combinations (parallel+block, batch+block)

## Notes

✅ PREREQUISITE UPDATE: Type enums completed in mlld-b4f. ForDirective.meta.actionType discriminator ('single' | 'block') verified in AST. ForDirective.meta.block.statementCount field validated. DirectiveSubtype includes 'for'. All regression tests pass. Ready for interpreter implementation once mlld-zeo (export let functions) is complete.


