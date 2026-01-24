---
id: mlld-gyv0
status: closed
deps: [mlld-zhdm, mlld-k0of]
links: []
created: 2025-12-21T02:15:10.139517-08:00
type: task
priority: 3
---
# Grammar: @var?"..." for strings

Add grammar for conditional string fragments.

**File:** `grammar/base/unified-quotes.peggy`

**Pattern:**
```peggy
ConditionalStringFragment
  = varRef:ConditionalVariableReference '"' parts:UnifiedDoubleQuotedContent* '"' {
      return {
        type: 'ConditionalStringFragment',
        condition: varRef.variable,
        content: parts,
        location: location()
      };
    }
```

**Example:**
```mlld
/var @greeting = "Hello " @title?"@title " "@name"
# "Hello Dr. Smith" or "Hello Smith"
```


