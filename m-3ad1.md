---
id: m-3ad1
status: open
deps: []
created: 2026-02-03T05:58:22Z
type: bug
priority: 2
assignee: Adam
tags: [security, env-directive, phase-3]
updated: 2026-02-03T05:58:25Z
---
# Tool restrictions not enforced in env blocks

The env directive allows setting tools: ['Read', 'Write'] to restrict available tools, but these restrictions are NOT enforced at runtime. isToolAllowed() exists in Environment.ts:937 but is never called during command execution.

Evidence:
- setAllowedTools() is called in env.ts:144
- isToolAllowed() exists in Environment.ts:937
- But no code calls isToolAllowed() before executing commands in run.ts or command-execution.ts

Test case: env @config [run cmd { echo 'Bash was NOT blocked!' }] with tools: ['Read', 'Write'] outputs 'Bash was NOT blocked!' instead of throwing an error.

Impact: Security configuration is syntax-only; env tool restrictions provide no actual security.


**2026-02-03 06:20 UTC:** sandbox-demo.mld updated to accurately document enforcement status. Filesystem, network, and resource limits are enforced BY DOCKER PROVIDER (not mlld-level). Tool restrictions remain NOT ENFORCED at mlld level - isToolAllowed() exists but is not called. Job ready for completion with these known limitations.

**2026-02-03 06:21 UTC:** sandbox-demo.mld updated to accurately document enforcement status. Filesystem, network, and resource limits are enforced BY DOCKER PROVIDER (not mlld-level). Tool restrictions remain NOT ENFORCED at mlld level - isToolAllowed() exists but is not called. Job ready for completion with these known limitations.
