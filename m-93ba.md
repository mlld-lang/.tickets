---
id: m-93ba
status: closed
deps: []
created: 2026-02-16T17:17:12Z
type: task
priority: 0
assignee: Adam
tags: [docs, p0, qa-driven]
updated: 2026-02-16T17:28:27Z
---
# doc: clarify tool-call-tracking allowed/denied require env blocks

## Problem

tool-call-tracking.md documents @mx.tools.allowed and @mx.tools.denied but doesn't explain they're only populated inside env blocks with tool restrictions. QA agents tested at top level and concluded the features were unimplemented ('phantom features'). They're real — just scoped to env blocks.

## Fix

Add clarification that @mx.tools.allowed and @mx.tools.denied arrays are populated by env blocks with tool restrictions. At top level (no env block), they're empty arrays.

## File
docs/src/atoms/security/tool-call-tracking.md

