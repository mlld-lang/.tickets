---
id: m-cf0f
status: open
deps: [m-f013]
created: 2026-02-18T17:09:31Z
type: task
priority: 0
assignee: Adam Avenir
updated: 2026-02-18T17:09:40Z
---
# Phase 6B: Activate checkpoint short-circuit semantics

Implement Phase 6B cache-hit short-circuit activation.

Scope:
- Activate real cache-hit short-circuit behavior for directive/exec/effect paths.
- Set checkpoint context fields (`@mx.checkpoint.hit`, `@mx.checkpoint.key`).
- Enforce interaction semantics:
  - user hooks run on hit and miss
  - guards run only on miss
  - user after-hooks still run on hit
- Verify observability pattern on hit/miss:
  - telemetry hooks using `state://` fire in both paths.

## Design

Hot-path files:
- `interpreter/eval/directive.ts`
- `interpreter/eval/exec/guard-policy.ts`
- `interpreter/eval/pipeline/builtin-effects.ts`
- checkpoint pre/post hook implementations

## Acceptance Criteria

Exit criteria:
- Added test coverage:
  - miss->hit behavior
  - guard skip on hit
  - user hooks run on hit/miss
  - checkpoint write on miss only
  - per-item `for parallel` caching
  - `state://` telemetry hook behavior on hit and miss
- All tests pass:
  - `npm test tests/interpreter/checkpoint/ tests/interpreter/hooks/ tests/interpreter/guards/`
  - `npm run build`
  - `npm test`
- Docs updates complete:
  - `docs/dev/HOOKS.md`
  - `docs/user/reference.md`
  - `docs/user/security.md`
- Commit made after green tests:
  - `feat(checkpoint): enable cache-hit short-circuiting with checkpoint hook semantics`

