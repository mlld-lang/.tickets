---
id: m-df57
status: open
deps: []
created: 2026-02-20T16:05:52Z
type: chore
priority: 3
assignee: Adam Avenir
tags: [severity-medium, polish]
updated: 2026-02-20T16:05:59Z
---
# Remove or implement HookScope — dead data on HookDefinition

## Problem

HookScope (perInput/perOperation/perFunction) is stored on every HookDefinition but never read during hook resolution. grep for scope access in user-hook-runner.ts returns zero matches.

## Options

1. Remove HookScope entirely (simplest — it's dead code)
2. Implement scope-aware resolution if there's a real use case

## Evidence

- HookScope defined and stored on registration
- user-hook-runner.ts never checks scope during matching

