---
id: mlld-k53
status: closed
deps: [mlld-76c]
links: []
created: 2025-12-13T05:32:55.2151-08:00
type: bug
priority: 1
---
# cmd and run lang {} blocks not tokenized inside exe/for blocks

Code blocks inside exe/for/when blocks aren't being tokenized.

Not working:
- for parallel(3) @r in @agents [ run cmd { bdm post } ]
- exe @func = [ run js { console.log('hi') } ]

Should work same as top-level:
- /run cmd { bdm post } ✓ works
- /run js { console.log('hi') } ✓ works

Issue: Nested run directives inside blocks not getting tokenized.

Files: Check how run directives are visited when inside block statements

## Notes

**ROOT CAUSE IDENTIFIED**

The tokenization code is correct, but highlighting fails when parse errors occur.

**Discovery:**
- With 'run @function()' inside blocks → parse error → broken tokenization
- Without 'run' (valid syntax) → parse succeeds → highlighting works ✅

**Conclusion:**
The tokenization fix I added (CommandVisitor.tokenizeCodeWithVariables) is correct and working.
The highlighting issues only appear when invalid 'run' keywords cause parse failures.

**Resolution:**
Once mlld-93g (Grammar: Support explicit 'run' keyword inside exe blocks) is fixed, the tokenization will work correctly because parsing will succeed.

**Status:**
Tokenization code: ✅ Fixed and working
Highlighting issues: Blocked by mlld-93g (grammar enhancement needed)


