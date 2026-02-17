---
id: m-f4d7
status: closed
deps: []
created: 2026-02-15T15:49:21Z
type: chore
priority: 3
assignee: Adam
tags: [qa-2026-02-14, p3, code-cleanup, policy]
updated: 2026-02-15T17:15:06Z
---
# Code cleanup: m-28de dead export matchesCommandPatterns

capability-patterns.ts still exports matchesCommandPatterns but nothing imports it. Remove or mark deprecated.

Also: inferCapabilityRule doesn't use specificity (inconsistent with evaluateCommandAccess). Should align or document why they differ.

## Acceptance Criteria

Dead export removed, inferCapabilityRule consistency addressed

