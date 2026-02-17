---
id: m-ff34
status: open
deps: []
created: 2026-02-13T21:49:07Z
type: feature
priority: 2
assignee: Adam
external-ref: mlld
---
# using auth:* should work with sh/js/py/node blocks, not just cmd

Currently `using auth:*` only works with `cmd {}` blocks. This is limiting because `cmd {}` does direct `@variable` string interpolation, which breaks with complex multi-line prompts containing special characters. `sh {}` blocks use env vars (`$variable`) which handle quoting safely, but can't use `using auth:*` for keychain-backed secrets.

The workaround is inlining `security find-generic-password` in `sh {}` blocks, which is macOS-specific and bypasses the mlld policy system entirely.

`using auth:*` should inject the env var into the execution environment of any block type — sh, js, node, py — not just cmd.

## Acceptance Criteria

- `sh { ... } using auth:name` injects the auth env var into the shell environment
- `js { ... } using auth:name` makes it available via process.env
- Existing `cmd { ... } using auth:name` continues to work
- Policy checks (keychain allow/deny, label flow) still apply across all block types

