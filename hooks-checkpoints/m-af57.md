---
id: m-af57
status: closed
deps: [m-2e1f]
created: 2026-02-18T17:09:31Z
type: task
priority: 1
assignee: Adam Avenir
updated: 2026-02-19T14:10:35Z
---
# Phase 9: Final docs + integration fixtures + coverage readiness

Implement Phase 9 final docs, fixture integration, and coverage audit.

Scope:
- Final doc sweep for dev/user docs touched by this initiative.
- Add end-to-end integration fixtures covering:
  - hooks+checkpoint
  - checkpoint+guards
  - resume+parallel loops
  - fork behavior
  - hook observability patterns (`state://`, external emission via existing directives) with non-fatal failure behavior
- Run coverage and close remaining gaps introduced by prior phases.

## Design

This is release-readiness hardening.
Ensure docs examples parse/execute via fixture pipeline and changelog is consolidated.

## Acceptance Criteria

Exit criteria:
- Added test coverage:
  - integration fixtures in `tests/cases/integration/*` for all key combinations above
- All tests pass:
  - `npm run build:fixtures`
  - `npm run build`
  - `npm test`
  - `npm run test:coverage`
- Docs updates complete:
  - `docs/dev/ARCHITECTURE.md`, `docs/dev/GRAMMAR.md`, `docs/dev/TESTS.md`, `docs/dev/HOOKS.md`
  - `docs/user/cli.md`, `docs/user/reference.md`, `docs/user/flow-control.md`, `docs/user/security.md`
- Commit made after green tests:
  - `docs(testing): finalize hooks-checkpoint-resume docs, fixtures, and coverage gates`

