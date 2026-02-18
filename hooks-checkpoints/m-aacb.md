---
id: m-aacb
status: open
deps: []
created: 2026-02-18T17:09:31Z
type: task
priority: 1
assignee: Adam Avenir
---
# Phase 0: Baseline + protocol contracts

Implement Phase 0 from `plan-hooks-checkpoints-resume.md`.

Context:
- This phase is a pre-implementation contract lock for hook/checkpoint semantics.
- It is required before grammar/runtime work to avoid churn in Phases 3 and 6.

Scope:
- Freeze decisions for:
  1) checkpoint pre-hook short-circuit protocol (`metadata` vs `fulfill` action)
  2) canonical operation type keys for hook filters (`for`, `for:iteration`, `for:batch`, `loop`, `import`)
- Add a short design note under `docs/dev` documenting selected decisions and rationale.
- Add/maintain `## [Unreleased]` at top of `CHANGELOG.md`.

## Design

Use existing hook architecture docs and runtime types as the source of truth:
- `docs/dev/HOOKS.md`
- `interpreter/hooks/HookManager.ts`
- `interpreter/hooks/hook-decision-handler.ts`
- `interpreter/env/ContextManager.ts`

Keep this phase documentation-only + baseline validation. Do not start behavior changes yet.

## Acceptance Criteria

Exit criteria:
- Added test coverage: baseline validation commands run and recorded for this phase.
- All tests pass:
  - `npm run build`
  - `npm test`
- Docs updates complete:
  - new/updated design note in `docs/dev/*` with selected protocol decisions
  - `CHANGELOG.md` has `## [Unreleased]`
- Commit made after green tests:
  - `chore(runtime): establish hooks-checkpoint implementation contract and unreleased changelog`

