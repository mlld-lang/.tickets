---
id: m-8ccd
status: closed
deps: []
created: 2026-02-15T08:10:24Z
type: chore
priority: 2
tags: [qa-2026-02-14, docs, policy]
updated: 2026-02-15T10:02:41Z
---
# Remove defaults.unlabeled auto-labeling claim from docs

Decision: remove the `defaults.unlabeled: "untrusted"` auto-labeling claim from docs. It's documented as auto-labeling unlabeled variables but doesn't actually work. Remove the claim rather than implement.

QA topic: policy-label-flow

## Acceptance Criteria

Docs no longer claim defaults.unlabeled auto-labels variables

**2026-02-15 10:02 UTC:** Removed all docs claims/usages of defaults.unlabeled auto-labeling across security docs and examples; replaced guidance with explicit labels/defaults.rules. Updated docs fixture source for security example parity. Validation: npm test (384 passed, 8 skipped).
