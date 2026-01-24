---
id: mlld-25m
status: closed
deps: []
links: []
created: 2025-12-17T17:26:24.3089-08:00
type: feature
priority: 2
---
# Add .replace() builtin method for strings

The .replace() and .replaceAll() builtin methods are ALREADY IMPLEMENTED in interpreter/eval/exec-invocation.ts (lines 244-256).

Current status:
- .replace(old, new) - replaces first occurrence (like JS replace)
- .replaceAll(old, new) - replaces all occurrences (like JS replaceAll)

What's missing:
- Test coverage in tests/cases/feat/builtin-methods-string-replace/
- Documentation in user docs

The feature exists and works, just needs tests and docs.


