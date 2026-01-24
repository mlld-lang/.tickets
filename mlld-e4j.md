---
id: mlld-e4j
status: closed
deps: []
links: []
created: 2025-12-10T19:40:11.908742-08:00
type: bug
priority: 2
---
# Improve error message when .att parsed as field access

When users write `@x.name.att` intending a path like `alice.att`, they get a confusing error:

"Cannot access field att on non-object value (string)"

This is because the parser interprets `.att` as field access on `@x.name`.

**Expected**: A helpful error message suggesting the user might need to escape the dot: `@x.name\.att`

**Current**: Generic field access error that doesn't hint at the likely cause.

This is a common gotcha for users working with .att template files in dynamic paths.

Reported by @partydev in chat.


