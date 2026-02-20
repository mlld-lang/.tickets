---
id: m-a1d7
status: open
deps: []
created: 2026-02-20T16:05:53Z
type: bug
priority: 3
assignee: Adam Avenir
tags: [severity-low, polish]
updated: 2026-02-20T16:05:59Z
---
# Fix default output format inconsistency between docs and code

## Problem

Documentation says the default output format is xml, but the code defaults to markdown. This is a 3-way inconsistency between docs, spec, and implementation.

## Work

1. Determine which is correct (markdown is likely the intended default)
2. Update docs to match the code

