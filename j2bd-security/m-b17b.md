---
id: m-b17b
status: closed
deps: []
created: 2026-02-03T05:53:20Z
type: task
priority: 2
assignee: Adam
tags: [phase-3, verification]
updated: 2026-02-03T06:00:33Z
---
# Phase 3: Verify sandbox restrictions are enforced by mlld runtime

Verify the sandbox-demo.mld restrictions are actually enforced at runtime:

1. Run sandbox-demo.mld end-to-end
2. Verify `env @config [ ... ]` block syntax executes correctly
3. Test that tool restrictions prevent Bash usage inside env block
4. Test that filesystem limits are enforced (read/write to restricted paths)
5. Test that network restrictions are enforced (net: 'none')
6. Test that credentials via 'using auth:name' are not readable as strings
7. Identify any gaps where mlld doesn't enforce these restrictions
8. Create friction tickets for any gaps found

Note: The sandbox-demo.mld shows the *configuration syntax*. This ticket verifies whether mlld's runtime actually *enforces* these restrictions or if they depend on external providers (like @mlld/env-docker) that may not be implemented yet.

