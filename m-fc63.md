---
id: m-fc63
status: in_progress
deps: []
created: 2026-01-31T00:57:15Z
type: feature
priority: 0
assignee: Adam
tags: [grammar, security, guards, size-m, complexity-m, risk-m, impl-none]
updated: 2026-01-31T10:48:36Z
---
# Implement with { privileged: true } syntax for user-defined guards

The spec describes guards with `with { privileged: true }` but this isn't implemented. Currently only policy-generated guards are privileged (hardcoded in core/policy/guards.ts). Privileged guards can: (1) remove protected labels like secret, untrusted, src:*, (2) cannot be bypassed with `with { guards: false }`. Implementation needs: grammar support for `with { privileged: true }` after guard body, and interpreter support to set the privileged flag from parsed AST.


**2026-02-09 22:01 UTC:** Also add grammar-level syntactic sugar: `guard privileged @name ...` as sugar for `guard @name ... with { privileged: true }`. The `guard privileged` form matches how the spec and docs already describe it (e.g. dual-audit-airlock job, pattern-dual-audit atom). Both forms should work.
