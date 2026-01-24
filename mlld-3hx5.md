---
id: mlld-3hx5
status: closed
deps: [mlld-zhdm, mlld-k0of]
links: []
created: 2025-12-21T02:15:16.830513-08:00
type: task
priority: 3
---
# Grammar: @var? in arrays

Add grammar for conditional array elements.

**File:** `grammar/patterns/array.peggy`

**Pattern:**
```peggy
ConditionalArrayElement
  = varRef:ConditionalVariableReference {
      return {
        type: 'ConditionalArrayElement',
        condition: varRef.variable,
        value: varRef.variable,
        location: location()
      };
    }
```

**Integration:** Add to `ArrayValue` alternatives (before regular variable references).

**Example:**
```mlld
/var @items = [@required, @optional?, @alsoRequired]
# If @optional is falsy, array is [@required, @alsoRequired]
```


