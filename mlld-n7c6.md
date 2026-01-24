---
id: mlld-n7c6
status: closed
deps: []
links: []
created: 2026-01-14T15:15:48.618839-08:00
type: task
priority: 1
parent: mlld-als4
---
# Phase 1.1: Rename mlld env to mlld vars

## Summary
Rename the existing `mlld env` CLI command to `mlld vars` to free up the `env` namespace for environment modules.

## Context
The current `mlld env` command manages environment variable allowlists (which env vars can be accessed in mlld scripts). Environment modules are a new feature that will use `mlld env` for spawning AI agents with configured environments.

## Implementation

### Files to Modify
1. `cli/commands/env.ts` → `cli/commands/vars.ts`
   - Rename file
   - Rename `envCommand` to `varsCommand`
   - Update all internal function names

2. `cli/commands/env.test.ts` → `cli/commands/vars.test.ts`
   - Rename file
   - Update test descriptions

3. `cli/execution/CommandDispatcher.ts`
   - Line 13: Update import from `./env` to `./vars`
   - Line 56: Change `'env'` to `'vars'` in command map
   - Line 96: Change `'envCommand'` to `'varsCommand'`

4. `cli/execution/index.ts` (if it re-exports)
   - Update any re-exports

### Search for Other References
```bash
grep -r "mlld env" docs/
grep -r "'env'" cli/
```

## Exit Criteria
- [ ] `mlld vars list` works (shows current env allowlist)
- [ ] `mlld vars allow VAR_NAME` works
- [ ] `mlld vars deny VAR_NAME` works
- [ ] `mlld env` returns "Unknown command" or similar
- [ ] All existing env command tests pass under new name
- [ ] No references to old command remain in docs
- [ ] Build succeeds with no type errors

## Tests
Update existing tests in `cli/commands/vars.test.ts` (renamed from env.test.ts)

## Dependencies
None - this is the first step

## Estimated Effort
1 hour


