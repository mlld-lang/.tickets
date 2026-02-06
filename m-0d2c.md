---
id: m-0d2c
status: closed
deps: []
created: 2026-01-30T04:51:51Z
type: bug
priority: 2
assignee: Adam
updated: 2026-01-30T12:04:08Z
---
# mlld <file> should support custom flags like mlld run does

When running `mlld run <script> --flag value`, flags are passed to the script via `@payload`. But running `mlld <file> --flag value` fails with 'Unknown option'.

**Expected:** Both invocation styles should support custom flags passed to scripts.

**Actual:** `mlld tmp/merge-verified.mld --run 2026-01-29-5` fails with 'Unknown option: --run'

**Suggestion:** `mlld run` should be a shortcut for `llm/run/*` scripts, but `mlld <file>` should have the same capabilities for custom args/flags via `@payload`.

