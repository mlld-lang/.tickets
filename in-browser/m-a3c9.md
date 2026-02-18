---
id: m-a3c9
status: open
deps: [m-0c82]
created: 2026-02-18T17:16:46Z
type: epic
priority: 1
assignee: Adam Avenir
updated: 2026-02-18T17:18:25Z
---
# Phase 4: Public browser SDK createRuntime contract

## Goal
Ship a stable browser SDK entrypoint (`createRuntime`) with clear API and typing contracts aligned to spec.

## Context and references
- Planning source: `plan-in-browser.md` (Phase 4).
- Product scope: `vision-in-browser.md`, `spec-in-browser.md`.
- Primary integration targets:
  - `sdk/browser.ts` (new)
  - `sdk/types.ts`
  - `interpreter/index.ts` (if profile selection changes are required)

## Required implementation
1. Add `sdk/browser.ts` exposing `createRuntime(options?)`.
2. Ensure runtime API contract includes:
   - `fs`
   - `run(path)`
   - `interpret(path, options)`
   - `analyze(path)`
3. Export/update browser runtime option/result types.
4. Ensure browser SDK entry does not import Node defaults (Node FS/path/context builders).

## Required test coverage
1. Add `sdk/browser.test.ts` covering:
   - basic run from virtual file
   - structured/debug mode behavior
   - unsupported executor denial behavior
   - dynamic module injection
2. Add API-shape contract tests for returned runtime object.
3. Run all touched suites and verify pass status.

## Required docs and changelog updates
1. Update `docs/user/sdk.md` with browser runtime section and examples.
2. Update `docs/dev/SDK.md` with browser entrypoint and contract notes.
3. Update `CHANGELOG.md` with Added entry for browser SDK runtime API.

## Commit requirement
- Commit only after tests are green.
- Commit message must be:
  - `feat(browser): add createRuntime sdk surface`

## Exit criteria
1. Browser SDK entrypoint and types are implemented and exported.
2. New and updated SDK tests are present and passing.
3. API behavior matches documented contract and examples.
4. Required docs and `CHANGELOG.md` updates are completed.
5. Required phase commit exists and references the completed test run.
