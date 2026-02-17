---
id: m-ef09
status: closed
deps: []
created: 2026-02-15T08:10:14Z
type: bug
priority: 2
assignee: Adam
tags: [qa-2026-02-14, p2, parser]
updated: 2026-02-15T10:35:05Z
---
# Parser accepts << without whitespace, corrupts state, breaks subsequent >> comments

var @z = 3<<No space (no whitespace before <<) is accepted, evaluates to 1, and corrupts parser state. Subsequent >> comments fail with misleading 'Text content not allowed in strict mode' error pointing at the wrong line.

QA topic: comments

## Acceptance Criteria

Parser rejects << without preceding whitespace, or handles it gracefully without state corruption


**2026-02-15 10:34 UTC:** Fixed parser ambiguity around unspaced inline comment markers. Updated comparison operator tokenization to reject doubled '<' and '>' as binary operators, tightened inline comment boundary checks, and added regression tests for unspaced << / >> plus multiline line-location behavior. Updated rc82 changelog entry. Full test suite passes (npm test). Commit: 52ac878a2.
