---
id: m-a27e
status: closed
deps: []
created: 2026-02-21T07:16:47Z
type: bug
priority: 0
assignee: Adam
updated: 2026-02-21T07:35:17Z
---
# Propagate sub-tool-call taint to parent exe output

When an agent exe (e.g. `exe llm @claude(prompt) = cmd { claude -p "@prompt" }`) calls a tool during execution (e.g. `@guardedFetch` with label `net:r`), the tool call's output taint does NOT propagate back to the parent exe's output.

Currently:
- Input data labels propagate (animal on @horse flows through) ✓
- Exe labels propagate (llm on @claude appears on output) ✓
- Tool call records propagate (@mx.tools.calls is shared via contextManager) ✓
- Tool call output taint does NOT propagate (net:r from @guardedFetch is lost) ✗

Root cause: `Environment.mergeChild()` (Environment.ts:2259-2268) merges variables but skips security state. Each invocation has its own `resultSecurityDescriptor` and `securityRuntime` — these are per-instance and never merged back.

Desired behavior: if the agent calls @guardedFetch during execution, the parent exe's output should carry `net:r` taint. The taint should track the actual execution path — what happened, not what was available.

This matters for downstream guards like:
```mlld
guard before secret = when [
  @mx.taint.includes("net:r") => deny "Network-influenced output cannot access secrets"
  * => allow
]
```

Without propagation, this guard can never fire on agent output that was shaped by a network read.

Key files:
- interpreter/env/Environment.ts:2259-2268 (mergeChild — no security merge)
- interpreter/env/Environment.ts:190,2757-2765 (securityRuntime per-instance)
- interpreter/eval/exec-invocation.ts:224-232 (resultSecurityDescriptor accumulation)
- interpreter/env/ContextManager.ts:266-268 (tool call recording — this part works)

Needs a live test (see docs/dev/TESTS.md) demonstrating the full propagation chain:
1. var animal @data = "..."
2. @data flows into exe llm @agent() which calls tool net:r @fetch()
3. Output carries: animal (input label), llm (exe label), net:r (tool call label)
4. net:r only appears when the agent actually calls the tool

## Acceptance Criteria

1. Sub-tool-call output taint merges into parent exe resultSecurityDescriptor
2. Labels only propagate if the tool was actually invoked (not just available)
3. Live test (skip-live.md gated) demonstrates: animal + llm + net:r propagation
4. Mocked fixture test demonstrates the same mechanism without live API calls
5. Existing tests continue to pass (no regression in current taint behavior)


**2026-02-21 07:29 UTC:** Implemented taint propagation for child environments by merging child security descriptors in Environment.mergeChild(), and merged invocation-scope execution descriptors into exec resultSecurityDescriptor in exec-invocation. Added env characterization coverage and new security fixtures: sub-tool-call-taint-propagation (mocked) + sub-tool-call-taint-propagation-live (skip-live gated). Verified with vitest on Environment.characterization, exec-invocation.structured, and interpreter fixture filtering for the new case.
