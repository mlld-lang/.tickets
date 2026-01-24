---
id: mlld-1sh
status: closed
deps: [mlld-cw9]
links: []
created: 2025-12-09T22:15:24.186936-08:00
type: task
priority: 1
---
# Phase 1.2: Grammar - Add While loop patterns with done/continue keywords

## Summary

Add while loop construct for bounded iteration with `done`/`continue` control keywords.

## Prereq

Complete abstraction discovery (mlld-cw9) first.

## Syntax

```mlld
# Pipeline stage
var @result = @initial | while(100) @processor

# Directive form
while (100, 1s) @processor

# Processor with done/continue
exe @processor(state) = when [
  @state.done => done @state.result
  * => continue @newState
]
```

## Files to Modify

- `grammar/base/literals.peggy` - Add DoneLiteral and ContinueLiteral
- `grammar/patterns/pipeline.peggy` - Add WhilePipelineStage
- `grammar/directives/while.peggy` (new file) - Add WhileDirective

## Implementation

### DoneLiteral and ContinueLiteral (in literals.peggy)

Follow exact pattern from `retry`, `skip`, `allow`, `denied` (lines 50-74):

```peggy
DoneLiteral "done literal"
  = "done" _ value:WhenRHSValue {
      return {
        type: 'ControlLiteral',
        subtype: 'done',
        values: { value: [value] },
        raw: { value: text() },
        meta: { hasValue: true }
      };
    }
  / "done" {
      return {
        type: 'ControlLiteral',
        subtype: 'done',
        values: { value: [] },
        raw: { value: 'done' },
        meta: { hasValue: false }
      };
    }

ContinueLiteral "continue literal"
  = "continue" _ value:WhenRHSValue {
      return {
        type: 'ControlLiteral',
        subtype: 'continue',
        values: { value: [value] },
        raw: { value: text() },
        meta: { hasValue: true }
      };
    }
  / "continue" {
      return {
        type: 'ControlLiteral',
        subtype: 'continue',
        values: { value: [] },
        raw: { value: 'continue' },
        meta: { hasValue: false }
      };
    }
```

### WhilePipelineStage

```peggy
WhilePipelineStage "while pipeline stage"
  = "while" _ "(" _ cap:Integer _ ("," _ rate:Duration)? _ ")" _ processor:ExeReference {
      return {
        type: 'WhileStage',
        values: {
          cap: cap,
          rate: rate || null,
          processor: processor
        },
        meta: {
          hasCap: true,
          hasRate: !!rate
        }
      };
    }
```

## Error Recovery

- Missing cap: "while() requires a maximum iteration count"
- Invalid processor: "while expects an executable reference like @processor"

## Context Object

While loops provide `@ctx.while.*`:
- `@ctx.while.iteration` - current iteration number (0-indexed)
- `@ctx.while.cap` - maximum iterations allowed

## Testing

```bash
npm run ast -- 'var @x = @y | while(10) @process'
npm run ast -- 'exe @p(s) = when [@s.done => done @s.result]'
```

## Validation

- [ ] DoneLiteral parses correctly
- [ ] ContinueLiteral parses correctly  
- [ ] WhilePipelineStage parses correctly
- [ ] Error messages are helpful

## Notes

Added while directive grammar, while pipeline stage, and done/continue literals. Directive list now includes /while; pipeline stage supports while(<cap>[,<rate>]) @processor with errors; done/continue literals parse via Literal valueType. Grammar build passes.


