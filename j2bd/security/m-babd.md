---
id: m-babd
status: closed
deps: []
created: 2026-02-09T08:41:30Z
type: bug
priority: 2
assignee: Adam Avenir
updated: 2026-02-09T22:25:49Z
---
# Policy deny rules not enforced for commands in exe functions

tests/cases/security/policy-deny-command-exec: Inline policy with deny: ['cmd:git:push'] doesn't enforce when command runs inside an exe function via cmd {}.


**2026-02-09 22:25 UTC:** Fixed command deny matching so multi-token deny patterns act as prefix matches (e.g., cmd:git:push denies git push origin main); added integration regression coverage and unskipped fixture policy-deny-command-exec.
