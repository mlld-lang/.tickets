---
id: m-a2ff
status: closed
deps: []
created: 2026-02-19T17:14:55Z
type: bug
priority: 0
assignee: Adam Avenir
external-ref: gh-551
updated: 2026-02-19T22:43:29Z
---
# CLI: hyphenated flags inaccessible via @payload dot notation

CLI flags with hyphens (e.g. --skip-live, --max-retries) produce payload keys that cannot be accessed via dot notation because - is not valid in mlld identifiers. No bracket-access syntax exists. This forces orchestrator authors to avoid the universal CLI convention of hyphenated flags. Needs a comprehensive durable solution: auto-convert to camelCase, bracket access syntax, or both.

## Acceptance Criteria

1. Hyphenated CLI flags are accessible from @payload without workarounds. 2. Solution handles multi-hyphen keys (--max-retry-count). 3. Existing scripts using non-hyphenated flags are unaffected.

