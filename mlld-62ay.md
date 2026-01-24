---
id: mlld-62ay
status: closed
deps: [mlld-jsio, mlld-4ml4, mlld-gyv0, mlld-k0of]
links: []
created: 2025-12-21T02:15:57.054427-08:00
type: task
priority: 3
---
# Interpreter: Handle @var?`...` in interpolation

Add interpolation handling for conditional template snippets.

**File:** `interpreter/utils/interpolation.ts`

In the interpolation loop (around line 157), add:
```typescript
} else if (node.type === 'ConditionalTemplateSnippet') {
  const { evaluateConditionalInclusion } = await import('../eval/conditional-inclusion');
  const { shouldInclude } = await evaluateConditionalInclusion(node.condition, env);
  
  if (shouldInclude) {
    const snippetContent = await interpolateImpl(node.content, env, context, options);
    pushPart(snippetContent);
  }
  // If falsy, skip entirely (no output)
}
```

Also handle `ConditionalStringFragment` with same logic.


