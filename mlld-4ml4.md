---
id: mlld-4ml4
status: closed
deps: [mlld-zhdm, mlld-k0of]
links: []
created: 2025-12-21T02:15:03.857474-08:00
type: task
priority: 3
---
# Grammar: @var?`...` for commands/templates

Add grammar for conditional template snippets in commands/templates.

**File:** `grammar/base/unified-quotes.peggy`

**Pattern:**
```peggy
ConditionalTemplateSnippet
  = varRef:ConditionalVariableReference "\`" content:UnifiedBacktickInterpolation* "\`" {
      return {
        type: 'ConditionalTemplateSnippet',
        condition: varRef.variable,
        content: content,
        location: location()
      };
    }
```

**Integration:** Add to `UnifiedBacktickInterpolation` alternatives.

**Example:**
```mlld
cmd { claude -p @tools?\`--allowedTools "@tools"\` }
```


