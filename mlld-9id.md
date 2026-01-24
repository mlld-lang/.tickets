---
id: mlld-9id
status: closed
deps: [mlld-0gd, mlld-zeo]
links: []
created: 2025-12-09T22:17:46.580094-08:00
type: task
priority: 1
---
# Phase 2.2: Interpreter - Add evaluateExeBlock() to exe.ts

## Summary

Add exe block evaluation to the interpreter, reusing the exported let/augmented assignment functions from when.ts.

## Key Insight

Exe blocks are ~80% the same as exe..when! An exe block is just a when expression without conditions (or with implicit `* =>`). Massive reuse opportunity.

## 📚 Required Reading

Before starting this task:
- **docs/dev/DATA.md** - StructuredValue system (.text, .data, .ctx)
- **interpreter/utils/structured-value.ts** - asData/asText helpers

Key patterns:
- All values flow as StructuredValue with `.text`, `.data`, `.ctx`
- `asData()` for computation boundaries (JS args, comparisons)
- `asText()` for display boundaries (templates, shell commands)
- Let assignments store StructuredValue wrappers
- Return values should preserve wrappers

## ❌ DON'T DO (Explicit Restrictions)

1. **Field access mutation in +=**: `let @data[-1].field += value`
   - Error: "ETOOCOMPLEX: Augmented assignment only supports simple variables."
   - Only support: `let @variable += value` (simple variable names)

2. **Multiple return statements**: `=> @x ... => @y`
   - Error at parse time: "Return statement must be last in exe block"

## Prereq

- mlld-0gd (Phase 1 verification)
- mlld-zeo (Export let functions)

## Files to Modify

- `interpreter/eval/exe.ts` - Add evaluateExeBlock() function

## Implementation

Add after existing exe evaluation code (around line 50):

```typescript
import { isLetAssignment, isAugmentedAssignment } from '@core/types/when';
import { evaluateLetAssignment, evaluateAugmentedAssignment } from './when';

async function evaluateExeBlock(
  block: ExeBlockNode,
  env: Environment,
  args: Record<string, unknown>
): Promise<unknown> {
  // Create child environment for block scope
  let blockEnv = env.createChild();

  // Bind parameters to arguments
  for (const [param, value] of Object.entries(args)) {
    const importer = new VariableImporter();
    const variable = importer.createVariableFromValue(
      param, 
      value, 
      'exe-param', 
      undefined, 
      { env: blockEnv }
    );
    blockEnv.setVariable(param, variable);
  }

  // Execute statements sequentially
  for (const stmt of block.values.statements) {
    if (isLetAssignment(stmt)) {
      blockEnv = await evaluateLetAssignment(stmt, blockEnv);
    } else if (isAugmentedAssignment(stmt)) {
      blockEnv = await evaluateAugmentedAssignment(stmt, blockEnv);
    } else {
      const result = await evaluate(stmt, blockEnv);
      blockEnv = result.env || blockEnv;
    }
  }

  // Handle return statement (required, must be last)
  if (block.values.return) {
    const returnResult = await evaluate(block.values.return, blockEnv);
    return returnResult.value;
  }

  return undefined;
}
```

## Wire into evaluateExe()

Update around line 500 to detect and handle ExeBlock:

```typescript
// After checking for when expressions, add:
if (content && content.type === 'ExeBlock') {
  return await evaluateExeBlock(content, env, boundArgs);
}
```

## Validation

- [ ] Block creates child environment
- [ ] Parameters bound correctly
- [ ] Let assignments create scoped variables (as StructuredValue)
- [ ] Augmented assignments work on arrays/strings/objects
- [ ] Return statement evaluates and returns value (preserving wrapper)
- [ ] Environment chaining preserved
- [ ] Field access mutation throws helpful error

## Notes

✅ PREREQUISITE UPDATE: Type enums completed in mlld-b4f. ExeBlockNode interface verified in core/types/exe.ts. DirectiveSubtype includes 'exeBlock'. Type guard available: isExeBlockNode(). All AST structures validated. Ready for interpreter implementation once mlld-zeo (export let functions) is complete.


