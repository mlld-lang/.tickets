---
id: mlld-zhdm
status: closed
deps: [mlld-k0of]
links: []
created: 2025-12-21T02:14:52.311365-08:00
type: task
priority: 3
---
# Grammar: ConditionalVariableReference shared pattern

Add shared grammar pattern for `@var?` conditional variable references.

**File:** `grammar/patterns/conditional-inclusion.peggy` (new)

**Pattern:**
```peggy
ConditionalVariableReference
  = "@" id:BaseIdentifier fields:AnyFieldAccess* "?" {
      return {
        type: 'ConditionalVarRef',
        variable: helpers.createVariableReferenceNode('varIdentifier', {
          identifier: id,
          ...(fields.length > 0 ? { fields } : {})
        }, location())
      };
    }
```

This shared pattern is used by all conditional inclusion contexts.


