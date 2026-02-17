---
id: m-0b3f
status: closed
deps: []
created: 2026-02-15T22:40:58Z
type: bug
priority: 0
assignee: Adam
updated: 2026-02-15T23:04:05Z
---
# Fix before op:cmd guards not firing for exe operations

## Problem

Guard filter `before op:cmd` fires for `/run cmd {}` directives but NOT for `exe @fn() = cmd {}` invocations.

**Test case that fails:**
```mlld
guard @test before op:cmd = when [
  * => deny "Blocked"
]

exe @fn() = cmd { echo "hello" }
show @fn()  >> Guard does NOT fire, command executes
```

**Test case that works:**
```mlld
guard @test before op:cmd = when [
  * => deny "Blocked"
]

run cmd { echo "hello" }  >> Guard DOES fire, command blocked
```

## Root Cause

File: `/Users/adam/dev/mlld/interpreter/hooks/guard-operation-keys.ts` function `buildOperationKeys` (lines 26-66)

The function only adds the bare `'cmd'` key when `operation.type === 'run'` (lines 34-42):
```typescript
if (operation.type === 'run') {
    if (runSubtype === 'runCommand') {
        keys.add('cmd');    // <-- only for type==='run'
    }
}
```

For exe operations (`type === 'exe'`), this special handling is skipped. The function only adds `opLabels` like `'op:cmd'`, but the grammar strips the `op:` prefix when parsing guards, so guards register as `'cmd'` (without prefix) and don't match.

Meanwhile, policy guards (in `core/policy/guards.ts` lines 154, 206) use `filterValue: 'op:cmd'` WITH the prefix, so they work for both run and exe.

## Fix

In `/Users/adam/dev/mlld/interpreter/hooks/guard-operation-keys.ts` function `buildOperationKeys`:

Add logic to extract bare command-type keys from `operation.opLabels` for exe operations:

```typescript
// After line 42 (after the run-specific handling):

// For exe operations, extract bare cmd-type keys from opLabels
if (operation.type === 'exe' && operation.opLabels) {
  for (const opLabel of operation.opLabels) {
    // Extract base command type: 'op:cmd' -> 'cmd', 'op:sh' -> 'sh'
    if (opLabel.startsWith('op:')) {
      const parts = opLabel.split(':');
      if (parts.length >= 2) {
        const cmdType = parts[1];  // 'cmd', 'sh', 'js', 'node', 'py'
        if (['cmd', 'sh', 'js', 'node', 'py'].includes(cmdType)) {
          keys.add(cmdType);
        }
      }
    }
  }
}
```

## Acceptance Criteria

1. Create test case in `tests/cases/security/` or `tests/cases/feat/security/`:
   ```mlld
   guard @blockCmd before op:cmd = when [
     * => deny "Commands blocked"
   ]

   exe @test() = cmd { echo "hello" }
   show @test()
   ```
   Expected: Throws error with message containing "Commands blocked"

2. Verify existing `/run cmd {}` behavior still works (regression test)

3. Verify the fix works for all command types: `op:cmd`, `op:sh`, `op:js`, `op:node`, `op:py`

4. Run `npm test` - all existing tests must pass

5. Update docs: No changes needed - `security-getting-started.md` already uses this pattern correctly

## Related Files

- `/Users/adam/dev/mlld/interpreter/hooks/guard-operation-keys.ts` (lines 26-66) - THE FIX GOES HERE
- `/Users/adam/dev/mlld/grammar/directives/guard.peggy` (lines 148-158) - Shows grammar strips `op:` prefix
- `/Users/adam/dev/mlld/core/policy/guards.ts` (lines 154, 206) - Policy guards use `'op:cmd'` with prefix (why they work)
- `/Users/adam/dev/mlld/qa/runs/2026-02-15-2/topics/security-getting-started/05-L-level3-basic-guard/` - QA experiment that found this
- Investigation: agent a7d2278 full report at `/private/tmp/claude-501/-Users-adam-dev-mlld/tasks/a7d2278.output`

**2026-02-15 23:04 UTC:** Fixed guard operation-key expansion for exe opLabels so before op:cmd matches exe command invocations. Added fixture feat/security/guard-op-cmd-exe and unit coverage for op:cmd/op:sh/op:js/op:node/op:py key extraction. Full npm test passes. Commit: 404ec4837.
