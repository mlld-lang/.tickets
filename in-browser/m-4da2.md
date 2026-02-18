---
id: m-4da2
status: open
deps: [m-60c1]
created: 2026-02-18T17:16:46Z
type: epic
priority: 1
assignee: Adam Avenir
updated: 2026-02-18T17:18:26Z
---
# Phase 7: Final docs sweep and release readiness

## Goal
Finalize all documentation, changelog, and release-readiness validation for the browser runtime rollout.

## Context and references
- Planning source: `plan-in-browser.md` (Phase 7).
- Full test and architecture references: `docs/dev/TESTS.md`, `docs/dev/ARCHITECTURE.md`.
- Required docs scope:
  - `docs/dev/ARCHITECTURE.md`
  - `docs/dev/TESTS.md`
  - `docs/dev/SDK.md`
  - `docs/dev/MLLDX.md`
  - `docs/user/sdk.md`
  - `docs/user/cli.md`
  - `README.md`
  - `CHANGELOG.md`

## Required implementation
1. Complete final consistency sweep across all dev/user docs and examples.
2. Ensure examples match final API/package/CLI naming (`mlld --ephemeral`, `mlld --ci`, `mlld/browser`).
3. Consolidate and clean final changelog entries in release section categories.
4. Confirm release notes and migration notes are internally consistent.

## Required test coverage
1. Add or adjust doc-backed tests/examples where feasible to prevent doc drift.
2. Execute the full project gate matrix and ensure all commands pass:
   - `npm run build:fixtures`
   - `npm run lint`
   - `TESTFAST=false npm test`
   - `npm run test:tokens`
   - `npm run test:heredoc`
   - `vitest run --config vitest.config.mlldx.mts`
   - `npm run test:browser`
   - `npm run build`

## Required docs and changelog updates
1. All listed docs must be updated and cross-checked for consistency.
2. `CHANGELOG.md` must contain complete browser-runtime release notes under Added/Changed/Fixed/Documentation.

## Commit requirement
- Commit only after the full matrix is green.
- Commit message must be:
  - `docs(browser): finalize browser runtime docs and changelog`

## Exit criteria
1. Full matrix passes with evidence in ticket notes or commit context.
2. All required docs are updated and internally consistent.
3. Changelog release notes are complete and accurate.
4. Required phase commit exists and references the completed full test run.
