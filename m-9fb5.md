---
id: m-9fb5
status: closed
deps: []
created: 2026-02-09T11:21:15Z
type: task
priority: 1
assignee: Adam Avenir
tags: [phase-3]
updated: 2026-02-09T11:22:43Z
---
# Verify defaults.rules documentation and implementation

Verify: (1) no-secret-exfil blocks secret output via classified operations, (2) no-untrusted-destructive blocks destructive ops, (3) error messages include rule name, (4) two-step pattern is clearly explained in docs. Check both atoms and impl/defaults-rules.mld.


**2026-02-09 11:22 UTC:** Verification complete: (1) no-secret-exfil blocking shown in labels-sensitivity.md:119-132 and defaults-rules.mld:66-79 - PASS. (2) no-untrusted-destructive blocking shown in labels-trust.md:45-61 and defaults-rules.mld:84-97 - PASS. (3) Error messages include rule names: labels-sensitivity.md:132 mentions no-secret-exfil, labels-trust.md:59 now mentions no-untrusted-destructive - PASS. (4) Two-step pattern clearly explained in policy-operations.md lead section and Why two steps? subsection - PASS.
