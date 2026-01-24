---
id: mlld-ait
status: closed
deps: [mlld-0gd]
links: []
created: 2025-12-09T22:18:37.290469-08:00
type: task
priority: 1
---
# Phase 2.4: Interpreter - Add while loop evaluator

## Summary

Add while loop evaluation for bounded iteration with done/continue control flow.

## 📚 Required Reading

Before starting this task:
- **docs/dev/DATA.md** - StructuredValue system (.text, .data, .ctx)
- **interpreter/utils/structured-value.ts** - asData/asText helpers

Key patterns:
- All values flow as StructuredValue with `.text`, `.data`, `.ctx`
- Pipeline stages receive/return StructuredValue
- State passed between iterations preserves wrappers
- done/continue values should preserve wrappers

## Prereq

- mlld-0gd (Phase 1 verification)

## Files to Create/Modify

- `interpreter/eval/while.ts` (new file)
- `interpreter/eval/pipeline/executor.ts` - Wire in while stage

## Context Variables (@ctx.while.*)

Processors have access to iteration context:

- `@ctx.while.iteration` (number) - Current iteration, **1-based**
- `@ctx.while.limit` (number) - Configured cap
- `@ctx.while.active` (boolean) - true when inside while loop

```mlld
exe @processor(state) = when [
  @ctx.while.iteration > 5 => done @state
  @ctx.while.iteration == @ctx.while.limit => done "hit cap"
  * => continue @state
]
```

## Pacing Semantics

- Parameter: `(cap, pacing)` e.g., `while(100, 1s)`
- Pacing is minimum delay BETWEEN iterations
- Applied after each iteration (except last)
- Uses same TimeDuration format as other mlld features (ms, s, m, h)

## Retry Interaction Rules (CRITICAL)

- Processors **CANNOT** use `retry` keyword (use `continue` instead)
- If processor returns `retry`, throw error: "Use 'continue' instead of 'retry' in while processors"
- Downstream stages CAN retry the entire while stage
- Example: `@initial | while(100) @iterate | @validate` (validate can retry the whole while)

## Implementation

### New File: while.ts

```typescript
import { Environment } from '@interpreter/env/Environment';
import { isDoneLiteral, isContinueLiteral, isRetryLiteral, WhilePipelineStage } from '@core/types';
import { MlldWhileError } from '@core/errors';
import { evaluate } from '../evaluate';

interface WhileContext {
  iteration: number;  // 1-based
  limit: number;
  active: boolean;
}

export async function evaluateWhileStage(
  stage: WhilePipelineStage,
  input: unknown,
  env: Environment
): Promise<unknown> {
  const cap = stage.values.cap;
  const rate = stage.values.rate;
  const processorRef = stage.values.processor;
  
  let state = input;
  
  for (let iteration = 1; iteration <= cap; iteration++) {
    // Create context for this iteration
    const whileCtx: WhileContext = { 
      iteration,  // 1-based
      limit: cap,
      active: true 
    };
    
    // Set up iteration environment with @ctx.while
    const iterEnv = env.createChild();
    iterEnv.setContextValue('while', whileCtx);
    
    // Invoke processor with current state
    const result = await invokeProcessor(processorRef, state, iterEnv);
    
    // Check for forbidden retry
    if (isRetryLiteral(result)) {
      throw new MlldWhileError(
        "Use 'continue' instead of 'retry' in while processors",
        { iteration, limit: cap }
      );
    }
    
    // Handle control flow
    if (isDoneLiteral(result)) {
      // Terminal - return the done value
      return result.values.value.length > 0 
        ? await evaluate(result.values.value[0], iterEnv)
        : state;
    }
    
    if (isContinueLiteral(result)) {
      // Continue with new state
      state = result.values.value.length > 0
        ? await evaluate(result.values.value[0], iterEnv)
        : state;
    } else {
      // Implicit continue with result as new state
      state = result;
    }
    
    // Apply pacing if specified (except on last iteration)
    if (rate && iteration < cap) {
      await delay(rate);
    }
  }
  
  // Reached cap without done - throw error with hint
  throw new MlldWhileError(
    `While loop reached cap (${cap}) without 'done'. Consider increasing cap or check termination logic.`,
    { iteration: cap, limit: cap }
  );
}
```

### Wire into Pipeline Executor

In `interpreter/eval/pipeline/executor.ts`, add handling for WhileStage:

```typescript
import { evaluateWhileStage } from '../while';

// In stage execution logic:
if (stage.type === 'WhileStage') {
  return await evaluateWhileStage(stage, input, env);
}
```

## Control Flow Semantics

- `done @value` - Terminate iteration, return value
- `done` (no value) - Terminate, return current state
- `continue @value` - Next iteration with value as new state
- `continue` (no value) - Next iteration with current state
- Implicit return - Treated as continue with result

## Error Handling

- **Cap exceeded**: `MlldWhileError` with hint to increase cap or check logic
- **Retry used**: Error telling to use `continue` instead
- **Invalid return**: Must return done/continue, error if neither recognized
- **Processor errors**: Propagate normally with iteration number in stack

## Validation

- [ ] While stage executes bounded iterations
- [ ] Done terminates and returns value (preserving StructuredValue)
- [ ] Continue advances to next iteration (preserving StructuredValue)
- [ ] Cap limit enforced with helpful error
- [ ] Rate limiting (pacing) works
- [ ] Context (@ctx.while.*) available in processor
- [ ] Retry throws helpful error message
- [ ] Iteration is 1-based (not 0-based)

## Notes

✅ PREREQUISITE UPDATE: Type enums completed in mlld-b4f. WhileDirective interface verified in core/types/while.ts (kind: 'while', subtype: 'while'). Pipeline stage type 'whileStage' validated in AST. DoneLiteralNode and ContinueLiteralNode interfaces exist in core/types/control.ts with type guards. DirectiveKind includes 'while', DirectiveSubtype includes 'while'. Ready for interpreter implementation.


