---
id: m-ba5a
status: closed
deps: []
created: 2026-02-09T13:34:25Z
type: task
priority: 2
assignee: Adam Avenir
tags: [phase-1, urgency-high]
updated: 2026-02-09T13:40:55Z
---
# Write pattern-dual-audit atom

Write the pattern-dual-audit atom documenting the two-call airlock pattern for prompt injection defense.

This is the capstone atom. It extends the single-auditor pattern (pattern-audit-guard) with an information bottleneck:
- Call 1 (tainted context): extracts instructions mechanically, no security decisions
- Call 2 (clean room): evaluates extracted summary against signed policy, returns verdict
- Privileged guard: blesses on safe verdict, denies otherwise

The atom should cover:
1. The information bottleneck concept (why two calls > one)
2. What each call does and sees
3. Signed templates verified via mlld verify
4. Privileged guard as the only path to bless tainted data
5. Working mlld code example using mock exes (like pattern-audit-guard does)

Target length: 150-250 words (this is a complex pattern, slightly longer is OK).
Must include at least one working example that passes `mlld validate`.

Related atoms: pattern-audit-guard, sign-verify, autosign-autoverify, labels-influenced, guards-privileged
File path: docs/src/atoms/security/pattern-dual-audit.md

