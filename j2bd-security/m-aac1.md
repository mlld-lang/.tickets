---
id: m-aac1
status: closed
deps: []
created: 2026-02-04T13:15:33Z
type: task
priority: 1
assignee: Adam
tags: [regression, urgency-high, phase-5-remediation]
updated: 2026-02-04T13:37:41Z
---
# Fix 12 test regressions from security label propagation changes

Security label propagation fixes introduced 12 test regressions. All were passing at d5b829d51. Four categories: (1) Strict-mode blank line loss - 7 tests - field-access.ts:787-795 wraps primitives in StructuredValue. (2) Delegate exe wrong value - exec-invocation.ts:3465-3483. (3) Nullish coalesce broken - StructuredValue wrapping interferes with ?? operator. (4) Guard retry failure - guard-inputs.ts changes. Must fix regressions while preserving security label propagation.

