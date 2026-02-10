---
id: m-7dfa
status: closed
deps: []
created: 2026-02-09T09:30:57Z
type: task
priority: 0
assignee: Adam Avenir
tags: [phase-3, bugfix, urgency-critical, security]
updated: 2026-02-09T09:38:41Z
---
# Fix post-hook guard bypass: privileged guards must survive with { guards: false }

BUG: guard-post-hook.ts line 182 handles disableAll by returning result immediately, skipping ALL guards including privileged ones. The pre-hook (guard-pre-hook.ts:441-442) correctly preserves privileged guards. Fix: In guard-post-hook.ts around line 182, instead of returning immediately on disableAll, filter to only privileged guards and continue execution if any exist. Pattern from pre-hook: guards.filter(def => def.privileged === true)

