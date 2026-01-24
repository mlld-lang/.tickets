---
id: mlld-2dbc
status: closed
deps: [mlld-jsio, mlld-ptfx, mlld-k0of]
links: []
created: 2025-12-21T02:16:10.752233-08:00
type: task
priority: 3
---
# Interpreter: Handle key?: in objects

Add object evaluation handling for conditional properties.

**File:** `interpreter/eval/data-values/CollectionEvaluator.ts`

In `evaluateObject` method:
```typescript
} else if (entry.type === 'conditionalPair') {
  const evaluated = await this.evaluateDataValue(entry.value, env, { suppressErrors: true });
  const { isTruthy } = await import('../expression');
  
  if (isTruthy(evaluated)) {
    evaluatedObj[entry.key] = evaluated;
  }
  // If falsy, omit the key entirely
}
```


