---
id: m-4ccf
status: closed
deps: []
created: 2026-02-05T18:48:22Z
type: task
priority: 2
assignee: Adam
tags: [phase-1, documentation]
updated: 2026-02-05T18:54:41Z
---
# Write policy-operations atom

## Context

The Success Criteria requires a `policy-operations` atom explaining operation risk classification.

The Target Example shows:
```mlld
operations: {
  "net:w": exfil,
  "op:cmd:rm": destructive
}
```

However, investigation shows:
1. `policy.operations` block may not be implemented (no matches in interpreter/*.ts)
2. The working approach uses direct exe labels: `exe exfil @postToServer(data)`
3. Grammar only accepts simple identifiers for exe labels (not `net:w` with colon)

## Task

Write the policy-operations atom documenting how operation risk classification works in mlld:

1. **If policy.operations IS implemented**: Document the two-step pattern (semantic labels on exe + policy classification)
2. **If policy.operations is NOT implemented**: Document the current working approach (direct risk labels on exe functions)

Include working, validated examples. The atom should be 100-200 words.

## Investigation Needed

Before writing, verify:
- Check if `policy.operations` parsing exists in grammar/directives/policy.peggy
- Check if operation classification logic exists in interpreter/eval/policy.ts
- Test if `operations: { ... }` in policy config has any effect

## Location

docs/src/atoms/security/policy-operations.md


**2026-02-05 18:54 UTC:** Phase 1 complete - all 5 required atoms exist. Ready to advance to Phase 2 implementation.
