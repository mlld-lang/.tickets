---
id: m-5eb7
status: open
deps: [m-1c21]
created: 2026-02-18T17:16:46Z
type: epic
priority: 1
assignee: Adam Avenir
updated: 2026-02-18T17:18:25Z
---
# Phase 2: Browser environment profile and executors

## Goal
Introduce an explicit browser runtime profile and JS-only executor policy while preserving current Node runtime behavior.

## Context and references
- Planning source: `plan-in-browser.md` (Phase 2).
- Architecture/testing references: `docs/dev/ARCHITECTURE.md`, `docs/dev/TESTS.md`.
- Primary integration targets:
  - `interpreter/env/Environment.ts`
  - `interpreter/env/bootstrap/EnvironmentBootstrap.ts`
  - `interpreter/env/executors/CommandExecutorFactory.ts`
  - `interpreter/env/executors/BrowserCommandExecutorFactory.ts` (new)
  - `interpreter/env/ImportResolver.ts`

## Required implementation
1. Add `BrowserCommandExecutorFactory` that allows only `js`/`javascript`.
2. Deny `bash`, `sh`, `node`, and `python` with `MlldSecurityError` in browser profile.
3. Add browser environment construction path (`BrowserEnvironment` or factory equivalent).
4. Ensure browser entry path does not import Node-only shadows/executors/resolvers.
5. Keep Node environment construction and behavior unchanged.

## Required test coverage
1. Add `BrowserCommandExecutorFactory` tests:
   - JS allowed path.
   - shell/node/python denial paths with exact error class checks.
2. Add browser environment profile tests proving initialization without Node-only deps.
3. Add regression tests confirming Node `Environment` behavior is unchanged.
4. Run all touched suites and verify pass status.

## Required docs and changelog updates
1. Update `docs/dev/ARCHITECTURE.md` runtime profile notes for browser host mode.
2. Update `docs/dev/TESTS.md` with browser-profile unit/integration test guidance.
3. Update `CHANGELOG.md` for Added/Fixed entries covering browser profile and security denials.

## Commit requirement
- Commit only after tests are green.
- Commit message must be:
  - `feat(browser): add browser environment and js-only executor profile`

## Exit criteria
1. Browser profile exists and is isolated from Node-only imports.
2. Unsupported executors are denied as `MlldSecurityError`.
3. New and updated tests are present and passing.
4. Required docs and `CHANGELOG.md` updates are completed.
5. Required phase commit exists and references the completed test run.
