---
id: m-a99e
status: closed
deps: []
created: 2026-02-09T16:14:38Z
type: task
priority: 2
assignee: Adam Avenir
tags: [urgency-high, doc-polish]
updated: 2026-02-09T16:16:52Z
---
# Bring 2 atoms to 100-word minimum for high polish

Two atoms are slightly below the 100-word minimum:

1. **policy-composition** (docs/src/atoms/security/policy-composition.md): 84 words, needs ~16 more. Add a sentence about what happens when auth configs from imported policies compose (union merge). This addresses the adversarial friction point about auth import behavior.

2. **modules-exporting** (docs/src/atoms/modules/exporting.md): 93 words, needs ~7 more. The 'Environment module pattern' section could note that callers import the policy separately to get credential configs.

Do NOT pad with fluff. Add substantive content that addresses the adversarial worker's friction points (auth composition behavior, import patterns).

