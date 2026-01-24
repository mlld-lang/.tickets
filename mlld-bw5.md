---
id: mlld-bw5
status: closed
deps: []
links: []
created: 2025-12-17T17:26:24.077681-08:00
type: feature
priority: 2
---
# Directory imports with auto-loading index.mld

Support importing entire directories that auto-load */index.mld files:

```mlld
/import "@agents" as @agents
// Returns: {party: {...}, mllddev: {...}}
// Each subdirectory's index.mld exports become fields

// Default skipDirs: ["_*", ".*"]
/import "@agents" as @agents with { skipDirs: [] }  // override
```

Use case: Multi-agent systems where each agent is a directory with index.mld defining its interface.

Requested by @partydev for scaffold pattern.


