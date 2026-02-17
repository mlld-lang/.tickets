---
id: m-abed
status: closed
deps: []
created: 2026-02-09T08:41:30Z
type: bug
priority: 2
assignee: Adam Avenir
updated: 2026-02-13T16:04:55Z
---
# For-loop dotted binding doesn't validate field existence

`for @file.path in @files` should throw when objects lack the `path` field, but currently silently succeeds.

Test fixture exists: `tests/cases/exceptions/for-dotted-missing-field/`
- `example.md`: iterates `@file.path` over objects with only a `name` field
- `error.md`: expects `Field "path" not found in object in for binding @file.path (key 0)`

The fixture defines the expected behavior. The interpreter needs to validate field existence during dotted binding iteration and throw the error that the test expects.
