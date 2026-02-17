---
id: m-0ba6
status: closed
deps: []
created: 2026-02-16T00:51:59Z
type: bug
priority: 0
assignee: Adam
updated: 2026-02-16T01:31:09Z
---
# Treat op:run and op:exe as aliases for guard dispatch

## Problem

A guard declared as `before op:run` fires for sh, node, and python subtypes but silently skips cmd. A user who writes `before op:run` expecting it to catch all command execution will miss cmd operations entirely. This is a security gap.

Additionally, `run cmd` does not get the `src:exec` label unless it's wrapped in an exe definition.

## What to do

Make `op:run` and `op:exe` behave as aliases in guard dispatch. The guard key that matters is the subtype (cmd, sh, js, node, py), not whether the directive was `run` or `exe`. A guard on `op:run` should fire for ALL run subtypes including cmd. A guard on `op:exe` should fire for the same set.

### Files to change

1. `interpreter/hooks/guard-operation-keys.ts` (line 26-74, `buildOperationKeys`):
   - When building keys for `op:cmd` operations (lines 35-55), also include the `op:run` key so that `before op:run` guards match cmd operations
   - When building keys for `op:exe` operations (lines 63-68), include both `op:exe` and `op:run` keys

2. `interpreter/eval/run-modules/run-exec-definition-dispatcher.ts`:
   - Line 243-248: cmd operations call `buildRunCommandOperationUpdate()`. Ensure the resulting opLabels include both `op:cmd` and `op:run`
   - Line 554-560: code operations (sh/py/node/js) already include `op:run`. Verify they also work with `op:exe` guards.

3. `interpreter/env/environment-provider.ts` (lines 102-123):
   - Ensure `src:exec` label is applied for direct `run cmd` operations, not only exe-wrapped ones

### Guard dispatch reference
- `interpreter/hooks/guard-candidate-selection.ts` (lines 62-99): collects guards by operation key
- `interpreter/eval/run-modules/run-policy-context.ts` (lines 26-52): builds operation labels for cmd

## Acceptance criteria

- `before op:run` fires for cmd, sh, node, py, js operations
- `before op:exe` fires for the same set
- `before op:cmd` continues to fire only for cmd operations (subtype-specific guards unchanged)
- Direct `run cmd` gets `src:exec` label
- Add test cases covering guard dispatch for each subtype with both op:run and op:exe guards


**2026-02-16 01:31 UTC:** Implemented in cab2bab99: op:run/op:exe alias dispatch for command-capable run/exe ops; run command op labels include op:run; added regression coverage in guard key tests and src:exec taint coverage for direct run cmd syntax. Verified with full npm test pass (4197 passed, 58 skipped).
