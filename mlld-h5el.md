---
id: mlld-h5el
status: closed
deps: [mlld-zhdm, mlld-k0of]
links: []
created: 2025-12-21T02:15:41.130551-08:00
type: task
priority: 3
---
# Types: Add conditional inclusion node types

Add TypeScript type definitions for conditional inclusion nodes.

**File:** `core/types/primitives.ts`

```typescript
export interface ConditionalTemplateSnippetNode extends BaseMlldNode {
  type: 'ConditionalTemplateSnippet';
  condition: VariableReferenceNode;
  content: BaseMlldNode[];
}

export interface ConditionalStringFragmentNode extends BaseMlldNode {
  type: 'ConditionalStringFragment';
  condition: VariableReferenceNode;
  content: BaseMlldNode[];
}

export interface ConditionalArrayElementNode extends BaseMlldNode {
  type: 'ConditionalArrayElement';
  condition: VariableReferenceNode;
  value: VariableReferenceNode;
}
```

**Also update:** `core/types/index.ts` to include in MlldNode union.


