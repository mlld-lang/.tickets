---
id: m-06ba
status: closed
deps: []
created: 2026-02-09T13:24:57Z
type: task
priority: 2
assignee: Adam Avenir
tags: [phase-1, doc, security]
updated: 2026-02-09T13:33:33Z
---
# Write guards-privileged atom

Write the guards-privileged atom at docs/src/atoms/security/guards-privileged.md.

Scope: How guards become privileged, what they can do that regular guards can't, and why this matters for security bottlenecks.

Key content:
1. Currently, only policy-generated guards are privileged (auto-created from policy rules and label flow config)
2. Privileged guards can: remove protected labels (secret, untrusted, src:*), bless via trusted!, clear! labels, cannot be bypassed with { guards: false }
3. Non-privileged guards CANNOT remove protected labels - attempting it throws PROTECTED_LABEL_REMOVAL error
4. This creates a security bottleneck: only policy-controlled code paths can clear taint
5. The with { privileged: true } syntax for user-defined guards is planned (m-fc63) but not yet available

Example should show a policy config that generates a privileged guard, and contrast with a non-privileged guard attempting the same operation.

Related atoms: label-modification, guards-basics, labels-trust, policies
Related code: core/policy/guards.ts, interpreter/hooks/guard-utils.ts, interpreter/eval/label-modification.ts

100-200 words, working mlld examples that pass mlld validate.

