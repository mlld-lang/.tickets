---
id: m-f8e0
status: closed
deps: []
created: 2026-02-15T08:10:03Z
type: bug
priority: 0
assignee: Adam
tags: [qa-2026-02-14, p0, pipelines]
updated: 2026-02-15T09:16:49Z
---
# Pipeline stages stringify return values, breaking numeric operations

Return values are stringified between pipeline stages. If @double returns number 10, @addTen receives string '10', so n + 10 becomes '1010' via string concatenation.

QA topic: pipelines-basics

## Acceptance Criteria

Numeric values pass through pipeline stages without stringification


**2026-02-15 08:42 UTC:** StructuredValue issue - see docs/dev/DATA.md. Pipeline stages should use asData() at computation boundaries to preserve numeric types, not stringify between stages.

**2026-02-15 09:16 UTC:** Preserved scalar pipeline stage values by auto-parsing JSON scalars in stage input binding for code-language stages and binding primitive structured inputs as primitive parameters. Added regressions for primitive bind behavior, scalar parse helper behavior, and end-to-end numeric pipeline stage chaining. Verified with npm test (4145 passed, 60 skipped).
