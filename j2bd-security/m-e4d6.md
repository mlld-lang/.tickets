---
id: m-e4d6
status: closed
deps: []
created: 2026-02-01T23:15:56Z
type: task
priority: 2
assignee: Adam
tags: [urgency-high, foundational]
updated: 2026-02-01T23:37:54Z
---
# Write sign-verify atom

Create documentation for the sign and verify primitives in mlld.

## Purpose
Document the sign and verify directives that form the core of template integrity checking.

## Key Concepts to Cover
- `sign` directive syntax: `sign @var by "signer" with algorithm`
- `verify` directive for checking signatures
- How signatures are stored (metadata)
- The verification flow in context of audit patterns
- What happens when verification fails

## Example from Job Spec
```mlld
var @auditCriteria = template "./prompts/audit-criteria.att"
sign @auditCriteria by "security-team" with sha256
```

## Dependencies
Depends on signing-overview for conceptual foundation.

## Location
docs/src/atoms/security/sign-verify.md


**2026-02-01 23:37 UTC:** Worker noted friction point: 'exe llm @name' syntax validation errors - may need follow-up for examples using this pattern.
