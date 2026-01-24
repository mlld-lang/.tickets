---
id: mlld-7j7
status: closed
deps: []
links: []
created: 2025-12-09T14:20:38.374747-08:00
type: bug
priority: 1
---
# Exe RHS pipeline syntax broken: value | cmd and cmd | cmd patterns fail

## Summary

Multiple  RHS pipeline patterns are failing to parse or execute correctly in strict mode.

## Expected Working Patterns

All of these should work:

```mlld
/exe @func(value) = @value | cmd { claude -p --model haiku }
/exe @func(value) = cmd { @value | claude -p --model haiku }
/exe @func(value) = @other(value) | cmd { claude -p --model haiku }
/exe @func(value) = js { return "hi" } | cmd { claude -p --model haiku }
/exe @func(value) = cmd { echo "hello" } | cmd { claude -p --model haiku }
/exe @func(value) = @value | cmd { claude -p } | cmd { claude -p "wdyt?" }
```

Bonus (if we can handle escaping):
```mlld
/exe @func(value) = cmd { claude -p "@prompt" --model haiku }
```

## Current Behavior

- `@value | cmd {...}` fails with "Invalid /exe syntax" parse error
- `run @value | cmd {...}` also fails
- User changed to `cmd { @value | claude... }` which should work but returns null/empty

## Why This Matters

**Piping is critical for large/complex content:**
- Escaping multi-line prompts with special chars is fragile
- Stdin piping is robust and standard Unix pattern
- Current audit script (audit-primitive-handling.mld) blocked by this

## Context

We use this pattern extensively:
- `scripts/mlld/audit-pathcontext-flow.mld` has `/exe @claude(prompt) = run @prompt | {claude -p}`
- But it's unclear if this actually works or silently fails
- We may have tests but they might be too simple (not testing large complex file content)

## Investigation Needed

1. Check grammar for exe RHS pipeline syntax - what's actually supported?
2. Test with real Claude calls and large file content
3. Verify existing tests actually cover this (not just toy examples)
4. Consider deprecating `run` in RHS position (accept but mark deprecated in grammar/CHANGELOG)

## Workaround

Unknown - need to investigate what actually works.

## Impact

Blocks parallel audit script and potentially other mlld-scripting-mlld patterns.


