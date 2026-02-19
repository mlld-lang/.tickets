---
id: m-b24a
status: open
deps: []
created: 2026-02-19T17:29:52Z
type: bug
priority: 0
assignee: Adam Avenir
---
# Audit and clarify @exists vs @fileExists built-in resolution

## Problem

The review orchestrator (`llm/run/review/index.mld`) used `@exists(@outPath)` assuming it was a built-in, but the actual helper is `@fileExists` (defined in `lib/context.mld` as a shell wrapper). The undefined `@exists(@outPath)` evaluated as truthy, silently skipping all LLM calls across all 6 phases — the orchestrator completed instantly producing zero results.

This exposed several gaps:

## Audit Scope

1. **Built-in function inventory**: Document every built-in function/method available in mlld (e.g., `@now`, `@root`, `@parse.*`, `@json`, etc.). Is `@exists` a built-in? If not, why does `@exists(@outPath)` evaluate as truthy instead of erroring?

2. **Undefined function call behavior**: When an undefined identifier like `@exists` is called with arguments, what should happen? Currently it appears to silently produce a truthy value. This should either:
   - Error clearly ("@exists is not a function")
   - Or be documented as intentional behavior with explanation of what value is produced

3. **File existence checking**: Clarify the canonical way to check if a file exists in mlld:
   - Is there a built-in? Should there be?
   - If the pattern is `exe @fileExists(path) = sh { test -f "$path" && echo "yes" || echo "no" }`, document it
   - Consider whether a built-in `@exists(path)` returning boolean would prevent this class of bug

4. **Documentation gaps**: Ensure `docs/user/reference.md` and any built-in reference clearly lists:
   - All built-in variables (`@now`, `@root`, `@mx.*`, etc.)
   - All built-in functions/methods
   - All built-in parsers (`@parse.llm`, `@parse.json`, etc.)
   - The file load operator (`<path>` and `<path>?`) vs runtime existence checks

## Evidence

- Bug found in: `llm/run/review/index.mld` lines 143, 200, 255, 311, 422, 490
- Correct usage in same file: lines 171, 226, 281, 337, 460 (`@fileExists` returning "yes"/"no")
- Fix applied: replaced `@exists(...)` with `@fileExists(...) == "yes"` 
- Root cause: no error on undefined function call, silent truthy evaluation

## Acceptance Criteria

- [ ] Undefined function calls either error or have clearly documented behavior
- [ ] All built-in functions, variables, and methods are inventoried in docs
- [ ] Canonical file-existence pattern is documented (built-in or recipe)
- [ ] Review orchestrator and other llm/run scripts audited for similar bugs

