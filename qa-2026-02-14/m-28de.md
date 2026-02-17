---
id: m-28de
status: closed
deps: []
created: 2026-02-15T08:44:55Z
type: bug
priority: 1
assignee: Adam
tags: [qa-2026-02-14, p1, policy]
updated: 2026-02-15T09:38:57Z
---
# Hierarchical op labels don't work in policy deny rules (op:cmd:git:add doesn't block git add)

Spec Section 1.5 says command labels are hierarchical (op:cmd:git:status, op:cmd:git, op:cmd) and Section 3.4 says prefix matching works. But deny: ["op:cmd:git:add"] does not block git add at runtime. Allowlist pattern (deny op:cmd:git, allow op:cmd:git:status) also doesn't restrict to allowed commands.

QA topic: policy-label-flow

## Acceptance Criteria

deny: [op:cmd:git:add] blocks git add; most-specific-wins matching works per spec


**2026-02-15 09:38 UTC:** Fixed policy command-pattern handling for hierarchical op labels: op:cmd:* forms now normalize to command patterns, and command allow/deny resolution uses best-match specificity (most-specific wins, deny on ties). Added regressions in core/policy/guards-command-access.test.ts and interpreter/eval/run.characterization.test.ts. Full npm test passes.
