---
id: m-c7d0
status: open
deps: []
created: 2026-02-19T23:02:01Z
type: bug
priority: 1
assignee: Adam Avenir
tags: [severity-high, pre-ga]
updated: 2026-02-19T23:02:06Z
---
# Fix CLI docs: mlld init incorrectly documents module creation flags

## Problem

`docs/user/cli.md:107-131` documents `mlld init` with module creation options (`--name`, `--author`, `--about`, `--version`, etc.) but `init` is actually a project initialization command. Module creation lives under `mlld module`/`mlld mod`.

## Evidence

- `init.ts:12-16`: init accepts only `--force`, `--script-dir`, `--local-path`
- `init-module.ts:22-34`: module creation with `--name`, `--author`, `--about`, etc.
- `CommandDispatcher.ts:56-58`: registers `init`, `module`, and `mod` as separate commands

## Fix

1. Correct the `mlld init` section to show actual project initialization options
2. Add a `mlld module` / `mlld mod` section with the metadata creation options

