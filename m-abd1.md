---
id: m-abd1
status: open
deps: []
created: 2026-02-09T11:41:30Z
type: task
priority: 1
assignee: Adam Avenir
tags: [phase-3, verification]
updated: 2026-02-09T11:41:31Z
---
# Phase 3: Verify two-step pattern end-to-end

Verify the two-step pattern implementation meets all Phase 3 success criteria: (1) no-secret-exfil blocks secret output via classified operations, (2) no-untrusted-destructive blocks destructive ops, (3) error messages include rule name, (4) two-step pattern is clearly explained in documentation. Run the demo at llm/run/j2bd/security/impl/defaults-rules.mld and verify output. Check error messages contain rule names. Review atoms for accuracy.

