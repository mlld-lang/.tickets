---
id: m-f92d
status: closed
deps: []
created: 2026-02-23T16:34:23Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [docs, gotchas]
updated: 2026-02-24T18:27:30Z
---
# Document that <@var> triggers file loading, not text output

## Problem

`<@var>` in templates triggers file loading, not text interpolation. This is the #1 syntax trap — QA agents repeatedly use `<@var>` expecting text output. It IS documented in gotchas but agents (and users) don't find it.

## Fix

1. Add a prominent callout to gotchas and intro: '`<@var>` always attempts file loading. For text output, use `@var` directly.'
2. Consider adding this hint to error messages when file-load fails on a variable reference that resolves to a non-path value.

