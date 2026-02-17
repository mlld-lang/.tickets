---
id: mlld-b1qi
status: closed
deps: []
links: []
created: 2026-01-04T13:09:25.627213-08:00
type: bug
priority: 2
tags: [size-s, complexity-m, risk-m, impl-none, needs-human-design]
updated: 2026-02-13T16:36:58Z
---
# Strict mode should not have implicit final output

In strict mode (.mld files), output should only come from explicit effects (show, log, output) as they're encountered. There should be no implicit final output of the script's return value.

Current behavior: Scripts appear to output both the explicit effect AND an implicit return value, causing duplication.

Expected behavior:
- **Strict mode (.mld)**: Only output from explicit directives (show, log, output). No implicit final output.
- **Markdown mode (.mld.md)**: May make sense to have implicitly built final result, but should still not duplicate with explicit outputs.

Discovered while testing llm/run/check-syntax-coverage.mld - using `show` caused duplicate output, switching to `log` fixed it.

Needs investigation:
1. Why does show cause duplication but log doesn't?
2. What's the intended semantic difference between show/log in this context?
3. Should strict mode have any implicit output at all?



**2026-02-13 16:36 UTC:** Folded into m-968f (top-level =>). The strict mode output model is now: show for side-effect stdout, log for stderr, => for script return value (printed by CLI), no implicit final output. This resolves the duplication bug.
