---
id: mlld-xc1h
status: closed
deps: [mlld-jsio, mlld-3hx5, mlld-k0of]
links: []
created: 2025-12-21T02:16:04.637559-08:00
type: task
priority: 3
---
# Interpreter: Handle @var? in arrays

Add array evaluation handling for conditional elements.

**File:** `interpreter/eval/data-values/CollectionEvaluator.ts`

In `evaluateArray` method:
```typescript
if (item?.type === 'ConditionalArrayElement') {
  const { evaluateConditionalInclusion } = await import('../conditional-inclusion');
  const { shouldInclude, value } = await evaluateConditionalInclusion(item.condition, env);
  
  if (shouldInclude) {
    evaluatedElements.push(value);
  }
  // If falsy, skip entirely
  continue;
}
```


