---
id: m-5bdc
status: closed
deps: [m-0989]
created: 2026-02-18T17:09:31Z
type: task
priority: 0
assignee: Adam Avenir
updated: 2026-02-19T08:07:01Z
---
# Phase 3B: User hook transforms + error isolation + emission compatibility

Implement Phase 3B transform + error isolation semantics.

Scope:
- Enable before/after transform chaining for user hooks.
- Add hook error isolation and `@mx.hooks.errors` collection.
- Add function-target matching with arg-prefix (`startsWith`) behavior.
- Validate external emission compatibility pattern (no new feature):
  - hook bodies can use existing directives/executables (`output`, `run`, `append`, `state://`, exe, MCP-backed exe)
  - side-effect failures in hook body are isolated and do not abort parent operation.

## Design

Core areas:
- user hook runner implementation
- directive/exec/effect integration paths
- context manager exposure for hook error arrays

Pattern validation should include state channel emission from hook bodies.

## Acceptance Criteria

Exit criteria:
- Added test coverage:
  - transform chain assertions (before/after)
  - hook error aggregation assertions (`@mx.hooks.errors`)
  - function + arg-prefix filter matching
  - hook `state://telemetry` emission succeeds
  - side-effect failure in hook body remains non-fatal and recorded
- All tests pass:
  - `npm test tests/interpreter/hooks/`
  - `npm run build`
  - `npm test`
- Docs updates complete:
  - `docs/dev/HOOKS.md`
  - `docs/user/security.md`
  - `docs/user/reference.md` (observability pattern notes)
- Commit made after green tests:
  - `feat(hooks): add chained transforms and isolated hook error collection`

