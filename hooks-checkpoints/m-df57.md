---
id: m-df57
status: closed
deps: []
created: 2026-02-20T16:05:52Z
type: chore
priority: 3
assignee: Adam Avenir
tags: [severity-medium, polish]
updated: 2026-02-24T19:46:50Z
---
# Remove or implement HookScope — dead data on HookDefinition

## Problem

`HookScope` (`core/types/hook.ts:4`) defines `'perInput' | 'perOperation' | 'perFunction'`. It is stored as the `scope` field on `HookDefinition` (`interpreter/hooks/HookRegistry.ts:15`). But `scope` is never read anywhere at runtime — `user-hook-runner.ts` does not reference `scope` or `HookScope` at all.

The grammar sets scope during parsing (`grammar/directives/hook.peggy:99` — `scope: 'perOperation'`), the value flows through to registration, and then it's ignored forever.

## Required change

Remove `HookScope` entirely — it's dead code with no consumer.

1. **Delete the `HookScope` type** from `core/types/hook.ts` (line 4). Remove its export if it's re-exported from an index file.
2. **Remove the `scope` field** from the `HookDefinition` interface in `interpreter/hooks/HookRegistry.ts` (line 15).
3. **Remove `scope` assignment** from the grammar in `grammar/directives/hook.peggy` (the `scope: 'perOperation'` in the `HookOperationFilter` rule, and any other filter rules that set `scope`).
4. **Remove any code** that reads or passes `scope` during hook registration (search for `scope` in `interpreter/hooks/` and `grammar/directives/hook.peggy`).
5. **Update tests** — any test that asserts on `scope` in a HookDefinition should have that assertion removed.

## Acceptance criteria

- `HookScope` type no longer exists in the codebase.
- `scope` field no longer exists on `HookDefinition`.
- Grammar no longer emits `scope` on hook filter nodes.
- All existing tests pass (after removing `scope` assertions).
- `npm run build` succeeds with no type errors.

