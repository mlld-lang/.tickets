---
id: mlld-9pl
status: closed
deps: [mlld-cw9]
links: []
created: 2025-12-09T22:14:45.573522-08:00
type: task
priority: 1
---
# Phase 1.1: Grammar - Add ExeStatementBlock and ForActionBlock patterns

## Summary

Add `[...]` block patterns to exe and for directives, reusing existing when-block infrastructure.

## 🔑 Critical Design Decision: `[...]` Not `{...}`

**Why blocks use `[...]` instead of `{...}`:**
- `{...}` is already used for code/commands/data objects
- `[...]` is consistent with when blocks (already use this)
- Zero ambiguity, no lookahead needed
- LLM-friendly: clear visual separation from JS/shell

**Array vs Block Disambiguation:**
- In exe/for context, `[...]` is **ALWAYS** a block
- Array literals only valid in `var` context
- Context determines meaning - no grammar tricks needed

**Examples:**
```mlld
exe @func() = [let @x = 1 => @x]    # Block (statements)
exe @func() = [1, 2, 3]              # Also block (3 expressions - likely error)
exe @arr() = {[1, 2, 3]}             # Escape hatch: object containing array
var @arr = [1, 2, 3]                 # Array literal (var context)
```

## ⚠️ IMPORTANT: Grammar Snippets Are Starting Points

The grammar snippets below are **starting points pending abstraction discovery findings** (mlld-cw9). The discovery phase may reveal better patterns to reuse. Don't copy these verbatim - use them as inspiration after understanding what already exists.

## Strict vs Loose Mode Clarification

- **Strict mode**: No slashes on directives (pure mlld)
- **Loose mode**: Requires `/` on top-level directives (markdown-friendly)
- **Inner statements**: Slashless in BOTH modes (inside blocks)

## Prereq

Complete abstraction discovery (mlld-cw9) first to understand what can be reused.

## Files to Modify

- `grammar/patterns/exe-rhs.peggy` - Add ExeBlockPattern
- `grammar/directives/for.peggy` - Add ForActionVariant
- `grammar/patterns/iteration.peggy` - Add ForBlockAction pattern

## Grammar Implementation (Starting Points)

### ExeBlockPattern (in exe-rhs.peggy)

Based on discovery, likely reuses `WhenExpressionEntry` + return statement:

```peggy
ExeStatementBlock "exe statement block"
  = "[" _ statements:ExeBlockStatementList _ returnStmt:ExeReturnStatement? _ "]" {
      return {
        type: 'ExeBlock',
        subtype: 'exeBlock',
        source: 'block',
        values: {
          statements: statements,
          ...(returnStmt ? { return: returnStmt } : {})
        },
        raw: {
          statements: helpers.reconstructRawString(statements),
          hasReturn: !!returnStmt
        },
        meta: {
          statementCount: statements.length,
          hasReturn: !!returnStmt
        }
      };
    }
```

### ExeBlockStatementList

Likely reuses existing patterns:

```peggy
ExeBlockStatementList
  = first:ExeBlockStatement rest:(_ stmt:ExeBlockStatement { return stmt; })* {
      return [first, ...rest];
    }

ExeBlockStatement "exe block statement"
  = LetAssignment              // let @x = value
  / AugmentedAssignment        // @x += value
  / EffectAction               // show, log, output, append
  / WhenRHSVarAssignment       // var @x = value (outer scope)
  / WhenRHSCommandAction       // cmd {...}
  / WhenRHSFunctionCall        // @func()
  / VarRHSContent              // Any expression
```

### ForActionVariant

```peggy
ForActionVariant
  = "=>" _ action:ForSingleAction {
      return { type: 'single', action: Array.isArray(action) ? action : [action] };
    }
  / "[" _ statements:ForBlockStatementList _ "]" {
      return { type: 'block', statements };
    }
```

### Update ExeRHSContent ordering

```peggy
ExeRHSContent "exe assignment value"
  = WhenExpression              # when [...]
  / ForExpressionExe            # for => [...]
  / ExeCodePattern              # js {...}
  / ExeCommandPattern           # cmd {...}
  / ExeDataPattern              # {key: value}
  / ExeStatementBlock           # NEW - [statements] (after data)
  / ... rest
```

## Error Recovery

Add helpful error messages for:
- Unterminated `[` - "Unterminated block. Expected ']' to close block."
- Comma separators - "Use newlines between statements, not commas"
- Multiple returns - "Only one return allowed as last statement"

## Statement Separator Rules

Use whitespace separation like when blocks:
- When.peggy lines 440-457 show the pattern
- Error helpfully on commas

## Testing

After building grammar:
```bash
npm run build:grammar
npm run ast -- 'exe @f() = [let @x = 1 => @x]'
npm run ast -- 'for @x in @xs => [show @x]'
```

## Validation

- [ ] ExeStatementBlock parses correctly
- [ ] ForActionBlock parses correctly  
- [ ] Error recovery works for common mistakes
- [ ] AST output matches type definitions
- [ ] Strict mode works (no leading /)
- [ ] Loose mode works (requires leading /)

## Notes

Grammar: added exe statement blocks and for block actions with block separators, return handling, and error recovery; ForActionVariant handles block vs single; ExeRHSContent routes to blocks. build:grammar/build passed.


