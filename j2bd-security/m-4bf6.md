---
id: m-4bf6
status: closed
deps: []
created: 2026-02-01T23:14:55Z
type: task
priority: 2
assignee: Adam
tags: [urgency-high, foundational]
updated: 2026-02-02T09:47:05Z
---
# Write signing-overview atom

Create documentation explaining why we sign templates in mlld.

## Purpose
Explain the security model for signed templates - why signing the template (control plane) rather than interpolated results protects against prompt injection.

## Key Concepts to Cover
- The threat model: LLM outputs can be influenced by prompt injection
- Why signing protects against instruction tampering
- The verification flow: orchestrator signs, auditor verifies
- How this fits with labels/policy/guards as defense-in-depth

## Context from Job
"Sign the template (control plane), not the interpolated result. The auditor calls verify to get the original signed instructions and compares against what's in its context. Injected content won't match."

## Location
docs/src/atoms/security/signing-overview.md


**2026-02-01 23:31 UTC:** Worker validated all code examples with mlld validate. Tests passing.

**2026-02-02 00:51 UTC:** Previous attempt returned description instead of content. File not committed. Needs redo.

**2026-02-02 06:08 UTC:** Verified atom exists with proper frontmatter and content explaining cryptographic integrity for templates.

**2026-02-02 09:47 UTC:** Verified atom exists with proper frontmatter and content explaining cryptographic integrity for templates.
