---
id: m-3ea7
status: closed
deps: []
created: 2026-02-16T00:52:15Z
type: chore
priority: 0
assignee: Adam
updated: 2026-02-16T01:34:43Z
---
# Fix guard @input.includes() examples in docs

## Problem

Several docs show `@input.includes('text')` in guard examples, but `@input` in a guard context is a StructuredValue, not a string. `.includes()` does not work on it. The correct pattern depends on what you're checking.

## What to do

### Fix these 3 files

1. **`docs/user/cookbook.md` line 141**: `let @hasQuestion = @input.includes("?")`
   - This is checking if a string parameter contains a character. If `@input` here is actually a function parameter (not a guard context), verify it works. If it's in a guard context, replace with the correct StructuredValue access.

2. **`docs/user/security.md` line 278**: `@input.includes("<script") => deny "Potentially malicious input"`
   - This is a guard checking input content. Replace with the correct StructuredValue access pattern. Check what field on the StructuredValue contains the text content (likely `.text` or `.data`), then use `.includes()` on that field.

3. **`docs/src/atoms/security/before-guards.md` line 30**: `@input.includes("sk-") => deny "Secrets cannot flow to run"`
   - Same fix as above. Replace with correct StructuredValue field access.

### Add Guard Context Reference

Add a section to `docs/src/atoms/security/guards-basics.md` (the `security-guards-basics` atom) showing what `@input`, `@output`, and `@mx` contain for each guard type. Show the actual structure so users know which fields to access.

Check the guard evaluation code to determine the exact structure of `@input` for before/after guards on different operation types (op:cmd, op:exe, op:run, etc.).

## Acceptance criteria

- No doc file contains `@input.includes(` in a guard context
- Each replaced example uses the correct StructuredValue field access
- `docs/src/atoms/security/guards-basics.md` has a Guard Context Reference section showing @input/@output/@mx structures
- Validate that the replacement patterns are correct by checking the guard evaluation code


**2026-02-16 01:34 UTC:** Implemented in 91f4f809f: replaced guard-context @input.includes() examples with @input[0] access, added Guard Context Reference section documenting @input/@output/@mx in docs/src/atoms/security/guards-basics.md, regenerated affected docs test cases. Verified with full npm test pass (4197 passed, 58 skipped).
