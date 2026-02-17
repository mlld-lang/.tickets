---
id: m-b31f
status: closed
deps: [m-9384]
created: 2026-02-12T03:22:29Z
type: task
priority: 0
assignee: Adam
updated: 2026-02-12T05:23:14Z
---
# Consolidation Program: Manual verification gate for Phase 3 guard scalpel

Manual verification gate required before any Phase 3 implementation. Validate guard pre/post semantic differences and approve only safe shared extractions.

## Acceptance Criteria

- Manually verify and record which guard helpers are safe to share and which must remain phase-specific.
- Explicitly record Go/No-Go decision for Phase 3 in ticket notes.
- No implementation for Phase 3 starts until this ticket is closed.
- If this gate phase includes a commit, run `npm test` and `npm run build`; both must pass before commit.


**2026-02-12 05:23 UTC:** --dir=refactor Manual verification complete (Phase 3 guard scalpel gate).

Compared guard pre/post behavior across:
- interpreter/hooks/guard-materialization.ts
- interpreter/hooks/guard-post-materialization.ts
- interpreter/hooks/guard-pre-hook.ts and guard-post-hook.ts call paths

Observed required semantic invariants:
1) Pre guard materialization must redact secret/sensitive input previews.
2) Post guard materialization previews intentionally do not apply pre redaction logic.
3) Per-input/per-operation preview branching stays pre-specific.
4) Retry/deny handling paths remain unchanged.

Approved shared-extraction scope (GO):
- shared truncate helper logic
- shared clone-with-input-system-flags logic
- shared clone-with-descriptor logic
- shared resolve-value fallback logic (parameterized by preview builder)

Out of scope / must remain phase-specific:
- any secret-label detection + redaction policy behavior
- pre-only buildInputPreview/perOperation handling
- post-only preview semantics

Manual gate decision: GO for Phase 3 limited to approved scope above.

Verification evidence:
- npx vitest run tests/interpreter/hooks/guard-materialization.test.ts tests/interpreter/hooks/guard-post-materialization.test.ts (pass)
- npx vitest run tests/interpreter/hooks/guard-pre-hook.test.ts tests/interpreter/hooks/guard-post-hook.test.ts (pass)
