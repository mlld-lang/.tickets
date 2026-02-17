---
id: m-7a9a
status: closed
deps: []
created: 2026-02-16T00:52:06Z
type: chore
priority: 0
assignee: Adam
updated: 2026-02-16T01:52:03Z
---
# Remove show { } object literal examples from docs

## Problem

The parser interprets `{ }` after `show` as a command block, not an object literal. `show { result: @parsed }` does not work. Several docs show this pattern.

## What to do

Search all files under `docs/` and `plugins/` for any `show { ... }` or `show {` patterns where curly braces follow show directly. Replace each with the var-then-show pattern:

```mlld
>> WRONG (doesn't parse):
show { result: @parsed }

>> CORRECT:
var @out = { result: @parsed }
show @out
```

### Known locations

The gotchas docs (`docs/src/atoms/mistakes/gotchas.md` and `plugins/mlld/skills/orchestrator/gotchas.md`) have already been fixed in this session.

Do a thorough grep for `show\s*\{` across all `.md`, `.mld`, and `.att` files in the repo to find any remaining instances. Fix each one.

## Acceptance criteria

- No doc or example file contains `show { ... }` as an inline object literal
- Each replaced instance uses the var-then-show pattern
- Validate changed `.mld` files with `mlld validate`


**2026-02-16 01:52 UTC:** Searched docs/ and plugins/ for inline show object-literal examples (show { key: value }) and found no remaining active examples; added regression test tests/integration/docs-show-object-literal-pattern.test.ts to enforce this invariant. Full test suite passes (4203 passed, 58 skipped).
