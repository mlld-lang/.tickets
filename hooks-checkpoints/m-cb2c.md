---
id: m-cb2c
status: closed
deps: []
created: 2026-02-19T23:02:01Z
type: bug
priority: 1
assignee: Adam Avenir
tags: [severity-medium, pre-merge]
updated: 2026-02-20T00:00:36Z
---
# Fix WhenExpression hook bodies always marking results as transformed

## Problem

WhenExpression hook bodies always return `{ transformed: true }`, causing observation-only when-based hooks to clobber the operation result. HookBlock bodies correctly check `hasReturn` but WhenExpression bodies do not.

## Evidence

- `user-hook-runner.ts:270-275`: WhenExpression always returns `{ transformed: true, value: evaluated.value }`
- `user-hook-runner.ts:278-293`: HookBlock correctly checks `body.meta?.hasReturn === true` before marking transformed
- The spec's first hook example (`hook @logLLM after op:exe = when [...]`) is observation-only and would break

## Fix

Add `hasReturn` check for WhenExpression bodies, matching the HookBlock behavior. Observation-only when hooks should return `{ transformed: false }`.

