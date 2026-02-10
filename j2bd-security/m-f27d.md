---
id: m-f27d
status: closed
deps: []
created: 2026-02-09T16:14:34Z
type: task
priority: 2
assignee: Adam Avenir
tags: [urgency-high, doc-polish]
updated: 2026-02-09T16:18:32Z
---
# Bring 2 atoms to 100-word minimum for high polish

Two atoms are slightly below the 100-word minimum:

1. policy-composition (docs/src/atoms/security/policy-composition.md): 84 words, needs ~16 more. Add a sentence about what happens when auth configs from imported policies compose (union merge).

2. modules-exporting (docs/src/atoms/modules/exporting.md): 93 words, needs ~7 more. Note that callers import the policy separately to get credential configs.

Do NOT pad with fluff. Add substantive content.


**2026-02-09 16:15 UTC:** Both atoms needing word count fixes (policy-composition and modules-exporting) are tracked under m-a99e.
