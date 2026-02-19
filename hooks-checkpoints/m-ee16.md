---
id: m-ee16
status: closed
deps: [m-6865]
created: 2026-02-18T17:09:31Z
type: task
priority: 1
assignee: Adam Avenir
updated: 2026-02-19T12:48:28Z
---
# Phase 5: CheckpointManager core

Implement Phase 5 CheckpointManager core.

Scope:
- Add persistent checkpoint manager with APIs:
  - load/get/put/clear/getStats
  - invalidation by function and fuzzy pattern
  - fork overlay read behavior (read-only source + local writes)
- Implement storage layout (`llm-cache.jsonl`, `manifest.json`, `results/*`).
- Ensure deterministic key computation with robust serialization fallback.
- Include corruption tolerance and compatibility handling.

## Design

Primary file:
- `interpreter/checkpoint/CheckpointManager.ts`

Include manifest version + atomic write strategy (temp file + rename) from pre-risk gates.

## Acceptance Criteria

Exit criteria:
- Added test coverage:
  - `tests/interpreter/checkpoint/CheckpointManager.test.ts`
  - determinism, persistence, invalidation, clear/fresh, fork read behavior
- All tests pass:
  - `npm test tests/interpreter/checkpoint/`
  - `npm run build`
  - `npm test`
- Docs updates complete:
  - `docs/dev/ARCHITECTURE.md`
  - `docs/dev/TESTS.md`
- Commit made after green tests:
  - `feat(checkpoint): add persistent checkpoint manager with invalidation APIs`

