---
id: m-ae0e
status: closed
deps: []
created: 2026-02-04T06:58:13Z
type: task
priority: 2
assignee: Adam
tags: [phase-1, doc]
updated: 2026-02-04T07:03:15Z
---
# Atom: policy-composition - combining policies and profiles

Write the policy-composition atom for docs/src/atoms/security/policy-composition.md. This atom should cover:

1. How multiple policies compose (intersection for allow, union for deny)
2. Profile selection based on composed policy requirements
3. Selective imports (import specific fields from a policy)

Refer to spec Part 8: Composition for the composition rules. The atom should be 100-200 words with working mlld code examples that pass `mlld validate`.

Existing related atoms to be aware of:
- security/policies.md - basic policy definition and import
- security/policy-auth.md - credential management
- security/policy-capabilities.md - capability restrictions

The code examples should show realistic scenarios like importing a baseline policy and adding local overrides.

