---
id: m-ead7
status: closed
deps: []
created: 2026-02-09T13:12:34Z
type: task
priority: 2
assignee: Adam Avenir
tags: [polish, high-polish]
updated: 2026-02-09T13:18:55Z
---
# Fix 6 polish gaps from excellence assessment

Excellence assessment (m-f5d0) found 6 polish gaps. Fix all:

1. **labels-trust.md:56** — bare `@wipe(@payload)` → `show @wipe(@payload)` (bare call doesn't trigger in markdown mode, silently succeeds instead of showing the promised error)

2. **labels-trust.md:8** — add `policy-operations` to the `related:` frontmatter field (atom directly uses the two-step pattern with operations mapping)

3. **policy-operations.md:8** — fix `guards-basics` → `security-guards-basics` in `related:` frontmatter (wrong atom id)

4. **labels-sensitivity.md:8** — fix `guards-basics` → `security-guards-basics` in `related:` frontmatter (wrong atom id)

5. **policy-operations.md** — add a 'Complete example' code block at the end showing a minimum viable two-step pattern that a user can copy-paste: policy config + 1 exe + 1 labeled var + show call that triggers the error. This matches the benchmark pattern in tool-reshaping.md.

6. **interpreter/policy/PolicyEnforcer.ts:147** — improve suggestion text. Currently says `Remove '${label}' label if data is not sensitive` for ALL non-source labels. But for `untrusted`, this is misleading (untrusted is about trust/provenance, not sensitivity). Change to label-specific phrasing:
   - For trust labels (untrusted): `Remove 'untrusted' label if data has been validated`
   - For sensitivity labels (secret, sensitive, pii): keep current text `Remove '${label}' label if data is not sensitive`
   - For other labels: `Remove '${label}' label if this classification no longer applies`

