---
id: m-74bd
status: closed
deps: []
links: []
created: 2026-01-29T04:46:33Z
type: bug
priority: 0
parent: 
assignee: Adam
tags: [interpreter, for-loop, filter]
updated: 2026-01-30T11:39:34Z
---
# for-when filter silently excludes items when accessing missing fields

When a for-when filter accesses a field that doesn't exist, the item is silently excluded instead of treating the missing field as falsy. Example: `for @r in @results when \!@r.error => @r` returns 0 items if objects don't have 'error' field, even though \!undefined should be truthy.

