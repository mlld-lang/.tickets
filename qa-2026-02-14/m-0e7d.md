---
id: m-0e7d
status: closed
deps: []
created: 2026-02-15T08:43:20Z
type: bug
priority: 1
tags: [qa-2026-02-14, p1, guards]
updated: 2026-02-15T09:35:11Z
---
# Guard after retry should work to retry the guarded operation

Clarification on retry behavior:
- `guard before` retry works in pipeline context -- that's correct
- `guard after` should be able to trigger a retry of the operation it's guarding (e.g., retry an LLM call if output doesn't validate)
- Currently errors with "guard retry requires pipeline context (non-pipeline retry deferred to Phase 7.3)"

QA topic: security-guard-composition

## Acceptance Criteria

guard after retry re-executes the guarded operation

**2026-02-15 09:35 UTC:** Implemented non-pipeline after-guard retry support for run-exec directives by marking runExec operation contexts as sourceRetryable in directive metadata. Added regression coverage in tests/interpreter/hooks/directive-hooks.test.ts (retries run exec directives when after guards request retry). Verified with targeted vitest runs and full npm test (pass).
