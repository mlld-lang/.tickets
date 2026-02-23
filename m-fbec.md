---
id: m-fbec
status: closed
deps: []
created: 2026-02-22T17:21:03Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [docs, gotchas]
updated: 2026-02-22T18:48:47Z
---
# Document empty array/object falsy behavior and truthiness rules

## What to add

Add a **truthiness rules table** to the gotchas howto page (`docs/src/atoms/howto/gotchas.md` or equivalent).

The table should list all falsy values in mlld:
- `false`
- `0`
- `""` (empty string)
- `null`
- `[]` (empty array)
- `{}` (empty object)

And note the key difference from JavaScript: in JS, `[]` and `{}` are truthy. In mlld, they are falsy. This is the most common source of confusion for JS-experienced users.

