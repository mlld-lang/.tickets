---
id: mlld-jsio
status: closed
deps: [mlld-h5el, mlld-k0of]
links: []
created: 2025-12-21T02:15:49.217352-08:00
type: task
priority: 3
---
# Interpreter: Shared conditional inclusion helper

Create shared helper for conditional inclusion evaluation.

**File:** `interpreter/eval/conditional-inclusion.ts` (new)

```typescript
import { isTruthy } from './expression';
import type { Environment } from '../env/Environment';

export async function evaluateConditionalInclusion(
  conditionNode: any,
  env: Environment
): Promise<{ shouldInclude: boolean; value: any }> {
  const { evaluate } = await import('../core/interpreter');
  
  const result = await evaluate(conditionNode, env, { isExpression: true });
  return {
    shouldInclude: isTruthy(result.value),
    value: result.value
  };
}
```

Uses existing `isTruthy()` from `interpreter/eval/expression.ts`.


