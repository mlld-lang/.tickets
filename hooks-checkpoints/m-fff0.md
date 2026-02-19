---
id: m-fff0
status: closed
deps: [m-cf0f]
created: 2026-02-18T17:09:31Z
type: task
priority: 1
assignee: Adam Avenir
updated: 2026-02-19T13:29:34Z
---
# Phase 7: CLI/SDK checkpoint-resume-fork wiring

Implement Phase 7 CLI/SDK wiring and checkpoint command surface.

Scope:
- Add run flags:
  - `--checkpoint`, `--fresh`, `--resume`, `--fork`
- Ensure these are treated as known flags and excluded from unknown-flag `@payload` injection.
- Add `mlld checkpoint` command group:
  - list/inspect/clean
- Wire options through CLI -> SDK -> interpreter entry points.

## Design

Primary files:
- `cli/commands/run.ts`
- `cli/execution/CommandDispatcher.ts`
- `cli/parsers/ArgumentParser.ts`
- `cli/index.ts`
- `sdk/execute.ts`, `sdk/types.ts`, `interpreter/index.ts`

## Acceptance Criteria

Exit criteria:
- Added test coverage:
  - run flag parsing/forwarding
  - payload exclusion assertions
  - checkpoint command tests (`list/inspect/clean`)
  - SDK option propagation tests
- All tests pass:
  - `npm test cli/commands/run.test.ts cli/commands/checkpoint.test.ts sdk/`
  - `npm run build`
  - `npm test`
- Docs updates complete:
  - `docs/user/cli.md`
  - `docs/user/reference.md`
- Commit made after green tests:
  - `feat(cli): add checkpoint/resume/fork run flags and checkpoint management commands`

