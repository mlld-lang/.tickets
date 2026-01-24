---
id: mlld-wrb
status: closed
deps: [mlld-cw9]
links: []
created: 2025-12-09T22:15:41.385926-08:00
type: task
priority: 1
---
# Phase 1.3: Grammar - Simplify when oneliner syntax

## Summary

Simplify when oneliner syntax to not require brackets around the condition.

## Current vs New Syntax

```mlld
# CURRENT (brackets around condition)
when [@condition] => action

# NEW (no brackets needed for oneliner)
when @condition => action

# Block form (unchanged)
when [
  @cond1 => action1
  @cond2 => action2
]
```

## Breaking Change

This simplifies the oneliner by removing unnecessary brackets. The block form remains unchanged.

## Files to Modify

- `grammar/directives/when.peggy` - Update oneliner pattern

## Implementation

The oneliner should parse as:

```peggy
WhenOneliner "when oneliner"
  = "when" _ condition:WhenCondition _ "=>" _ action:WhenAction {
      return {
        type: 'WhenExpression',
        subtype: 'oneliner',
        values: {
          conditions: [condition],
          actions: [action]
        },
        // ... 
      };
    }
```

Keep the block form parsing as-is.

## Rationale

- More concise syntax for simple conditionals
- Consistent with other oneliners in the language
- Block form still available for multi-condition cases

## Testing

```bash
npm run ast -- 'when @x > 5 => "big"'      # oneliner (new)
npm run ast -- 'when [@x > 5 => "big"]'    # block form (unchanged)
```

## Validation

- [ ] Oneliner parses without brackets
- [ ] Block form still works
- [ ] Both forms produce correct AST

## Notes

Simplified when simple form to accept bracketless oneliner. Grammar rebuild ok; block forms untouched.


