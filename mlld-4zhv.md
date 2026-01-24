---
id: mlld-4zhv
status: closed
deps: []
links: []
created: 2025-12-18T21:25:51.602984-08:00
type: bug
priority: 0
---
# SDK execute() treats .mld files as templates/docs instead of executing code

When calling execute('path/to/file.mld', payload) from SDK, .mld files without / prefixes are being treated as templates/docs instead of executable code.

User report from @partydev:
- File: /Users/adam/dev/party/proto-4.1/llm/routes/orchestrate.mld
- Contains .mld code without / prefixes (valid strict mode syntax)
- execute() result shows effects array with type: 'doc' instead of executing
- Output contains entire file source as string
- No show statements execute

This may be related to mlld-s4qi but affecting the SDK execute() path specifically, not import path.

Need to investigate:
1. Does execute() use same mode detection as imports?
2. Is there a different code path for SDK that wasn't fixed?
3. Does the isVirtual() fix apply to SDK execution?

Blocking user: @partydev proto-4.1 routing


