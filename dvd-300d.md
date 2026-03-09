---
id: dvd-300d
status: closed
deps: []
links: []
created: 2026-02-28T18:53:44Z
type: bug
priority: 0
assignee: Adam
tags: [cli, payload]
updated: 2026-02-28T22:32:24Z
---
# -e mode should accept payload flags

Running `mlld -e '...' --flag value` rejects unknown flags with 'Unknown option: --flag'. Payload flags should work the same as when running a script file — they get collected into @payload automatically. This is likely just a matter of passing unknown flags through to the payload object in the -e code path instead of rejecting them.

