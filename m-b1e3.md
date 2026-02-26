---
id: m-b1e3
status: closed
deps: []
created: 2026-02-24T16:51:07Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [security, policy]
updated: 2026-02-24T18:27:30Z
---
# Policy deny should cover all block types, not just cmd-namespace commands

## Current behavior

`deny: ['sh']` only blocks the external `sh` command via cmd namespace. It does NOT block native `run sh {}` blocks. Users who write `deny: ['sh']` believe they've blocked all shell access when they haven't.

## New design

`deny` should support blocking at the block-type level:

- `deny: ['sh']` blocks native sh blocks AND the sh command
- `deny: ['cmd']` blocks cmd blocks
- `deny: ['js']` blocks js blocks
- `deny: ['py']` blocks py blocks
- `deny: ['node']` blocks node blocks
- `deny: ['prose']` blocks prose blocks

For specific external commands, use colon-delimited patterns:
- `deny: ['cmd:echo']` blocks the echo command
- `deny: ['cmd:npm:run:*']` blocks npm run with any args
- `deny: ['cmd:rm:-rf:*']` blocks rm -rf with any args

The root-level names (sh, cmd, js, py, node, prose) are the full list of deniable block types.

