---
id: m-e5a7
status: closed
deps: []
created: 2026-02-25T06:21:44Z
type: task
priority: 1
assignee: Adam Avenir
updated: 2026-02-25T17:42:14Z
---
# Rename --env reserved CLI flag to --mlld-env

## Problem

`--env` is reserved for agent environment loading, but users naturally want `--env` for their own payload injection (e.g., `mlld run deploy --env prod`). Currently `--env` is silently consumed as a built-in flag, so `@payload.env` is never set.

## What to do

1. Find every place in the codebase where `--env` is treated as a reserved/built-in CLI flag for agent environment loading
2. Change all of those references from `env` to `mlld-env`
3. After the rename, `--env` must flow through to `@payload.env` like any other unknown flag
4. Update all tests that pass `--env` for agent environment loading to pass `--mlld-env` instead

## Acceptance criteria

1. `mlld run script --env prod` sets `@payload.env` to `"prod"` — it is no longer consumed as a built-in flag
2. `mlld script.mld --env staging` sets `@payload.env` to `"staging"`
3. `mlld run script --mlld-env agent-config.json` loads the agent environment (same behavior `--env` had before)
4. All existing tests pass: `npm test`

## Where to look

The CLI argument parser — likely `cli/parsers/ArgumentParser.ts` or the run/file command handlers. Search the codebase for the string `--env` or `'env'` in argument parsing code to find every reference.

## Note

The docs example in `cli-run` has already been updated to use `--environment prod` as an interim workaround. Once this rename lands, `--env` will work naturally for payload injection.
