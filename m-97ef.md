---
id: m-97ef
status: open
deps: []
created: 2026-02-25T19:58:50Z
type: bug
priority: 0
assignee: Adam Avenir
---
# RegistryManager.fetchLocked bypasses lock integrity

RegistryManager.ts:332-337 — fetchLocked() is called when a lock file specifies a locked version, but just returns the resolved path without actually fetching locked content. Also emits a console.warn to stderr. This means lock file integrity guarantees are not enforced.

