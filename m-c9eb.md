---
id: m-c9eb
status: closed
deps: []
created: 2026-02-25T19:15:30Z
type: chore
priority: 2
assignee: codex
updated: 2026-02-27T06:48:40Z
---
# Remove dead ConfigLoader system

The core/config/loader.ts ConfigLoader system (mlld.config.json) is fully dead code from an ancient security model with ttl/trust primitives no longer in the grammar. No mlld.config.json file exists anywhere. The active config system is ProjectConfig (mlld-config.json). Remove ConfigLoader, its types, tests, and clean up all ~15 files that import from it. Keep core/config/utils.ts (parseDuration/formatDuration used by cli/commands/run.ts) and core/config/logging.ts (used by logger).

