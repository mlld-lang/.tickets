---
id: m-ebd2
status: closed
deps: [m-aa6c]
created: 2026-02-15T08:10:33Z
type: chore
priority: 2
assignee: Adam
tags: [qa-2026-02-14, p2, docs, labels]
updated: 2026-02-15T10:16:04Z
---
# Docs: label-modification shows fake syntax (trusted!, clear!) that doesn't parse

The label-modification doc shows trusted!/clear! shorthand that doesn't parse. Actual syntax: with { addLabels, removeLabels }. Also: privileged only works on guards not exe functions, factual labels live in taint array not labels array.

QA topic: label-modification

## Acceptance Criteria

Doc shows working syntax, clarifies privileged scope and label locations


**2026-02-15 10:16 UTC:** Updated docs/src/atoms/security/label-modification.md to use real guard-action syntax (addLabels/removeLabels), clarify guard-only privileged scope, and clarify src:* provenance lives in .mx.taint. Added regression fixture security/guard-label-modification-with-clause. Full npm test passed. Commit: b427e8a55.
