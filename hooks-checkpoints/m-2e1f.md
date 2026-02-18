---
id: m-2e1f
status: open
deps: [m-fff0]
created: 2026-02-18T17:09:31Z
type: task
priority: 1
assignee: Adam Avenir
updated: 2026-02-18T17:09:40Z
---
# Phase 8: Resume targeting + fuzzy invalidation + fork semantics

Implement Phase 8 resume + fuzzy cursor + fork semantics.

Scope:
- Implement `--resume` behavior and targeted invalidation:
  - `@fn`
  - `@fn:index`
  - `@fn("prefix")`
- Implement invocation-site indexing for disambiguation.
- Implement `--fork` semantics with read-only source cache and local target writes.

## Design

Coordinate with checkpoint manager and CLI parsing semantics.
Focus on deterministic matching and invalidation behavior under script drift.

## Acceptance Criteria

Exit criteria:
- Added test coverage:
  - resume no-target
  - resume by function, by index, by fuzzy prefix
  - fork hit/miss matrix (model/prompt differences)
  - read-only source cache enforcement
- All tests pass:
  - `npm test tests/interpreter/checkpoint/`
  - `npm run build`
  - `npm test`
- Docs updates complete:
  - `docs/user/flow-control.md`
  - `docs/user/reference.md`
  - `docs/user/cli.md`
- Commit made after green tests:
  - `feat(checkpoint): implement resume targeting, fuzzy invalidation, and forked cache overlays`

