---
id: m-5fa8
status: open
deps: []
created: 2026-02-20T16:05:53Z
type: task
priority: 2
assignee: Adam Avenir
tags: [severity-medium, pre-ga]
updated: 2026-02-20T16:05:59Z
---
# Add hook naming syntax to reference docs

## Problem

docs/user/reference.md shows hook declaration syntax but omits the optional naming syntax. Users cannot see that hooks can be named (e.g., hook @myHookName after op:exe = [...]).

## Work

Update the hooks section of reference.md to show the named hook form and explain its purpose (identification in @mx.hooks.errors, debugging).

