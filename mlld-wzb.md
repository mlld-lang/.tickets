---
id: mlld-wzb
status: closed
deps: []
links: []
created: 2025-12-11T19:55:48.93822-08:00
type: bug
priority: 0
---
# Grammar bug: Second bare directive parsed as text in strict mode

In strict mode, when two directives appear on consecutive lines, the second directive is incorrectly parsed as text content and triggers 'Text content not allowed in strict mode' error.

## Reproduction
```mlld
run {echo "A"}
run {echo "B"}
```

Parse this with `{mode: 'strict'}` and it fails with:
```
Parse error: Text content not allowed in strict mode (.mld). Use .mld.md for prose. at line 2, column 4
```

## Expected
Both directives should be parsed and executed.

## Actual  
Second directive is treated as text and triggers strict mode error.

## Root Cause
The Directive rule predicate checks `isLogicalLineStart()` which requires the previous character (skipping whitespace) to be a newline. After the first directive's newline is consumed, the second directive starts at a position where the previous character is `\n`, which should satisfy `isLogicalLineStart()`. However, something in the parsing order causes the second directive to not match.

Happens even with simple directives, no blank lines needed - just any two bare directives on consecutive lines.

## Impact
- ~80 new strict mode test fixtures cannot be used until this is fixed
- Strict mode is essentially unusable for multi-line scripts
- BLOCKING: Cannot enable strict mode as default until fixed

## Workaround
For now, using slash prefix (`/run`, `/var`, etc.) works in strict mode since the Directive rule checks both `isStrictMode || hasSlash`.

## Files
- `grammar/mlld.peggy` - Directive rule line 216-246
- `grammar/deps/grammar-core.ts` - isLogicalLineStart helper line 146-151
- `grammar/base/whitespace.peggy` - may be related to newline handling


