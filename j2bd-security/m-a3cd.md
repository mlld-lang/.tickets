---
id: m-a3cd
status: closed
deps: []
created: 2026-02-03T05:47:36Z
type: task
priority: 2
assignee: Adam
tags: [phase-2, implementation]
updated: 2026-02-03T05:52:36Z
---
# Create sandbox demo restricting agent to Read/Write tools only

Create sandbox demonstration per Success Criteria Phase 2.

Required configs to demonstrate:
1. Sandbox config restricting tools to Read/Write only (no Bash)
2. Sandbox config limiting filesystem access to specific directories
3. Sandbox config disabling or limiting network access
4. Sandbox config with no MCP servers
5. Demonstrate credential injection that agent cannot read as string

Create at llm/run/j2bd/security/impl/sandbox-demo.mld (separate from existing audit guard demo).

Use env directive with policy.capabilities to restrict tools. Show how agent running inside env block has limited access.


**2026-02-03 05:52 UTC:** Phase 2 complete. Ready to create Phase 3 verification tickets.
