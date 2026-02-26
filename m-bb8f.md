---
id: m-bb8f
status: closed
deps: []
created: 2026-02-25T20:52:39Z
type: bug
priority: 0
assignee: Adam Avenir
updated: 2026-02-25T22:18:37Z
---
# Broken guard-composition tests exposed by recursive fixture walker

The recursive walker fix in build-fixtures.mjs exposed 15 guard-composition tests under feat/security/guard-composition/ that were previously unreachable (old code only recursed 2 levels deep). These tests have real issues:

- 5 tests have parse errors: shell redirection (>) in cmd {} blocks (hints-aggregation, import-flatten-order, registration-order, retry-shared-budget, trace-allow)
- 4 tests throw GuardErrors (deny-retry-precedence, multiple-deny, override-except, transform-then-deny)
- 3 tests have stale expected output (no-guards, transform-chain, transform-provenance, unnamed-deterministic)

Also exposed: feat/pipeline-retry/leading-effect-freshness (stale iteration counts), slash/import/input/stdin-compatibility (field access error), slash/show/add/exec-invocation-direct (JSON formatting changed from compact to pretty).

These need investigation and fixing — either the tests are wrong, the expectations are stale, or the underlying features have regressions.


**2026-02-25 21:08 UTC:** Investigation: These are test bugs, not mlld bugs. The guard-composition tests use /show and mlld directives inside cmd {} blocks, but cmd blocks are shell — so /show gets passed to /bin/sh which fails. The test authors incorrectly put mlld syntax inside shell blocks. Fix: rewrite the tests to use mlld directives outside cmd blocks. tk edit m-bb8f
