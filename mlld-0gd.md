---
id: mlld-0gd
status: closed
deps: [mlld-9pl, mlld-1sh, mlld-wrb, mlld-8d6, mlld-1iz]
links: []
created: 2025-12-09T22:16:16.299919-08:00
type: task
priority: 1
---
# Phase 1.5: Verify grammar with AST tool

## Summary

Validate all grammar changes by running the AST tool on representative examples.

## ⚡ Incremental Unblocking

This verification task can unblock Phase 2 work **incrementally**:

- Once exe block grammar is verified → mlld-9id (exe interpreter) can start
- Once for block grammar is verified → mlld-0ic (for interpreter) can start  
- Once while grammar is verified → mlld-ait (while interpreter) can start

Don't wait for all constructs to be verified before starting interpreter work on verified constructs.

## Prereq

All Phase 1 grammar issues must be complete:
- mlld-9pl (block patterns)
- mlld-1sh (while patterns)  
- mlld-wrb (when syntax)
- mlld-8d6 (types)
- mlld-1iz (error recovery)

## Build First

```bash
npm run build:grammar
```

## Test Cases

### Exe Block
```bash
npm run ast -- 'exe @f() = [let @x = 1 => @x]'
```

Expected: ExeBlock node with statements array and return value.

### For Block
```bash
npm run ast -- 'for @x in @xs => [show @x]'
```

Expected: ForDirective with meta.actionType = 'block'.

### When Oneliner (new syntax)
```bash
npm run ast -- 'when @x > 5 => "big"'
```

Expected: WhenExpression with oneliner subtype.

### When Block (unchanged)
```bash
npm run ast -- 'when [@x > 5 => "big"]'
```

Expected: WhenExpression with block subtype.

### While Pipeline Stage
```bash
npm run ast -- 'var @x = @y | while(10) @process'
```

Expected: Pipeline with WhileStage node.

### Done/Continue Literals
```bash
npm run ast -- 'exe @p(s) = when [@s.done => done @s.result * => continue @newState]'
```

Expected: ControlLiteral nodes with done/continue subtypes.

## Common Errors to Debug

- "Expected token but..." - Usually JavaScript in action block has unsupported syntax
- Type mismatches - Check core/types/ alignment
- Backtracking errors - Check rule ordering, more specific first

## Debug Mode

```bash
DEBUG_MLLD_GRAMMAR=1 npm run ast -- '/var @test = "value"'
```

## Validation Checklist

- [ ] Exe block parses correctly → **unblocks mlld-9id**
- [ ] For block parses correctly → **unblocks mlld-0ic**
- [ ] When oneliner parses correctly
- [ ] When block still works
- [ ] While stage parses correctly → **unblocks mlld-ait**
- [ ] Done literal parses correctly
- [ ] Continue literal parses correctly
- [ ] All AST output matches type definitions

## Notes

Ran AST validation after grammar build: /exe block with block+return, /for block action, when simple and block forms, while pipeline stage, and done/continue in when-expression inside /exe; all parsed as expected.


