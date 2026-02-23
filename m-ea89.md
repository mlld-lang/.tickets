---
id: m-ea89
status: closed
deps: []
created: 2026-02-22T17:21:18Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [docs]
updated: 2026-02-22T18:48:47Z
---
# Fix misleading doc claims: labels-trust, variables-conditional, labels-overview

Three separate doc fixes:

## 1. labels-trust.md — false claim about warning

`docs/src/atoms/security/labels-trust.md` claims a warning is logged when trusted and untrusted data conflict. No warning is actually emitted. Remove the claim about the warning, or just state the behavior without mentioning a warning.

## 2. variables-conditional.md — wrong cmd output example

`docs/src/atoms/syntax/variables-conditional.md` has a code comment showing `cmd` output with quoted values. `cmd` actually strips quotes from output. Fix the example comment to show unquoted output.

## 3. labels-overview.md — wrong @mx.sources description

`docs/src/atoms/security/labels-overview.md` describes `@mx.sources` as a "transformation trail." It actually only records file paths and guard names — not a full transformation history. Update the description to accurately reflect what `@mx.sources` contains.

