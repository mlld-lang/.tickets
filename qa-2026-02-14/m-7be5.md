---
id: m-7be5
status: closed
deps: []
created: 2026-02-15T08:09:52Z
type: bug
priority: 0
assignee: Adam
tags: [qa-2026-02-14, p0, labels]
updated: 2026-02-15T08:58:38Z
---
# @mx.op.labels.includes() errors on negative checks

Calling @mx.op.labels.includes('label') when the label is NOT present throws 'Unable to resolve object value for builtin method invocation' instead of returning false. Only works when the exact label IS present.

Workaround: use @mx.taint.includes() instead.

Docs actively recommend the broken syntax as the pattern to use.

QA topics: labels-overview, mcp-guards

## Acceptance Criteria

@mx.op.labels.includes() returns false for missing labels instead of throwing


**2026-02-15 08:58 UTC:** Normalized @mx.op context so op.labels always resolves to an array (merged labels+opLabels), preventing includes() throws on missing labels. Added guard-pre-hook regression for @mx.op.labels.includes('destructive') false path. Verified with npm test run (all passing).
