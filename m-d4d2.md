---
id: m-d4d2
status: open
deps: []
links: []
created: 2026-03-05T22:46:18Z
type: feature
priority: 1
assignee: Adam
tags: [nexus, guards]
---
# [User Request] Configurable max retries per guard

Guard max retries is currently hardcoded to 3 in guard-pre-runtime.ts (DEFAULT_GUARD_MAX). Should be configurable per-guard or at least per-policy. Syntax TBD -- could be a guard option, a policy setting, or both. Origin: Nexus team evaluation -- structured output validation retry loops need tunable retry counts depending on the complexity of the schema and the model being used.

