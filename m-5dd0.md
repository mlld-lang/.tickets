---
id: m-5dd0
status: closed
deps: []
created: 2026-02-21T01:47:54Z
type: bug
priority: 0
assignee: Adam
updated: 2026-02-21T11:44:50Z
---
# Guard @input.any.mx.* throws during var tools normalization

When a guard uses per-operation @input.any.mx.labels.includes() syntax (documented in guards-basics.md line 71) and the guarded exe is referenced in a var tools collection, the guard condition gets evaluated during tool collection normalization — before any actual tool call. At that point @input has no real data, so @input.any.mx throws 'Cannot access field mx on non-object'.

Repro: guarded-fetch.mld in plugins/mlld/examples/guarded-fetch/

Guard that triggers the bug:
  guard @noSecretExfil before net:r = when [
    @input.any.mx.labels.includes("secret") => deny "Secret data cannot flow to network operations"
    * => allow
  ]

var tools @tools = {
  guardedFetch: { mlld: @guardedFetch, labels: ["net:r"] }
}

Error: MlldCondition: Directive error (when): Failed to evaluate function in condition: includes
Root cause: Cannot access field mx on non-object

Either var tools normalization should not trigger guard evaluation, or @input.any.mx should gracefully return false/empty when no input context exists.

Workaround: use 'for secret' trigger (data-side guard) instead of 'before net:r' with @input.any.mx checks.

## Acceptance Criteria

1. Guards using @input.any.mx.labels in per-operation context do not throw during var tools normalization
2. Module with guarded exe + var tools collection loads without error
3. Guards still fire correctly when the tool is actually invoked via MCP


**2026-02-21 07:22 UTC:** Clarification on root cause vs defense-in-depth:

1. PRIMARY FIX: var tools normalization should not trigger guard evaluation. Guards are definitions that should only fire when an actual operation happens. The fact that defining a tool collection causes registered guards to evaluate is the root bug.

2. SECONDARY FIX (defense in depth): @input.any.mx.* should gracefully return false/empty instead of throwing when there's no input context. Like optional chaining — safe to evaluate even without data.

Fix 1 is the real fix. Fix 2 prevents similar issues elsewhere.

**2026-02-21 11:43 UTC:** Implemented fix: guard pre-hook now skips var tools normalization (isToolsCollection), preventing before net:r guard execution during tool definition. Added defense-in-depth by attaching guard quantifier helpers to both guard input variables and their object values so @input.any.mx.* chains remain safe when raw values are resolved. Added regressions in interpreter/eval/tools-collection.test.ts, tests/interpreter/hooks/guard-helper-injection.test.ts, and cli/mcp/MCPServer.test.ts; targeted vitest suites all pass.
