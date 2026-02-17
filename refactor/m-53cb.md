---
id: m-53cb
status: closed
deps: [m-b31f]
created: 2026-02-12T03:22:30Z
type: task
priority: 0
assignee: Adam
updated: 2026-02-12T05:25:48Z
---
# Consolidation Program: Phase 3 guard scalpel implementation (post verification)

Implement only the guard consolidations approved by manual verification: extract truly shared helpers while preserving intentional pre/post semantic differences.

## Acceptance Criteria

- Implement only items approved in the manual verification gate.
- Keep pre/post behavior differences intact where explicitly required.
- Before committing this phase, run `npm test` and `npm run build`; both must pass.


**2026-02-12 05:25 UTC:** --dir=refactor Implemented approved Phase 3 guard scalpel scope only.

Changes:
- Added shared helper module: interpreter/hooks/guard-materialization-shared.ts
- Refactored pre guard materialization wrappers to call shared helpers while preserving pre-only redaction/per-operation preview logic.
- Refactored post guard materialization wrappers to call shared helpers while preserving post preview semantics.

Behavior contract checks:
- Pre redaction semantics preserved.
- Post preview semantics preserved.
- Public function names/entry points in pre/post modules preserved.

Verification evidence:
- npx vitest run tests/interpreter/hooks/guard-materialization.test.ts tests/interpreter/hooks/guard-post-materialization.test.ts tests/interpreter/hooks/guard-helper-injection.test.ts tests/interpreter/hooks/guard-post-helper-injection.test.ts tests/interpreter/hooks/guard-pre-hook.test.ts tests/interpreter/hooks/guard-post-hook.test.ts (pass)
- npm test (pass) -> 360 passed | 7 skipped; 3935 passed | 63 skipped
- npm run build (pass)
