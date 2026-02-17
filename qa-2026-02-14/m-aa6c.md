---
id: m-aa6c
status: closed
deps: []
created: 2026-02-15T08:10:21Z
type: chore
priority: 2
tags: [qa-2026-02-14, docs, guards]
updated: 2026-02-15T10:07:38Z
---
# Document guard composition: last transform wins, always timing

Design decided: last transform wins for before guards. Document this along with:
- `always` timing takes precedence over before/after
- After guards chain sequentially
- Retry should work for guards per spec (Section 4.3) but currently errors with "guard retry requires pipeline context" -- separate bug

QA topics: security-before-guards, security-guard-composition

## Acceptance Criteria

Doc explains last-wins for before transforms, always timing, and sequential after-guard chaining

**2026-02-15 10:07 UTC:** Updated guard docs to explicitly document: before-transform last-wins behavior, always timing runs in both phases, and sequential after-guard chaining. Added regression fixture tests/cases/security/guard-composition-last-wins. Validation: npm test (384 passed, 8 skipped).
