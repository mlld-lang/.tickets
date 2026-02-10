---
id: m-9bf5
status: closed
deps: []
created: 2026-02-09T13:03:12Z
type: task
priority: 2
assignee: Adam Avenir
tags: [urgency-high, test-coverage]
updated: 2026-02-09T13:11:27Z
---
# Fix 8 security exception test fixtures: bare calls → show calls

## Problem

8 security exception test fixtures in tests/cases/exceptions/security/ use bare `@func(@arg)` invocations at the end of .mld files. This is invalid in strict mode — it produces a parse error ('Text content not allowed in strict mode') instead of the expected policy denial error. The tests pass because the test runner (interpreter.fixture.test.ts:1392) only checks that interpretation didn't succeed, not that the error message matches error.md.

## Files to Fix

All 8 files follow the same pattern — change the last line from bare `@func(@arg)` to `show @func(@arg)`:

1. `tests/cases/exceptions/security/no-secret-exfil/example.mld` — `@send(@token)` → `show @send(@token)`
2. `tests/cases/exceptions/security/no-secret-exfil-show/example.mld` — `@leak(@key)` → `show @leak(@key)`
3. `tests/cases/exceptions/security/no-secret-exfil-run-interpolation/example.mld` — `@leak()` → `show @leak()`
4. `tests/cases/exceptions/security/no-secret-exfil-using/example.mld` — `@send()` → `show @send()`
5. `tests/cases/exceptions/security/no-secret-exfil-defaults-override/example.mld` — `@send(@token)` → `show @send(@token)`. NOTE: This test has `labels: { secret: { allow: ["exfil"] } }` which OVERRIDES the deny. Check what the expected behavior should be: the allow should win over the built-in rule, meaning this test should SUCCEED (no error). Check this case carefully.
6. `tests/cases/exceptions/security/no-sensitive-exfil/example.mld` — `@send(@payload)` → `show @send(@payload)`
7. `tests/cases/exceptions/security/no-untrusted-destructive/example.mld` — `@wipe(@payload)` → `show @wipe(@payload)`
8. `tests/cases/exceptions/security/no-untrusted-privileged/example.mld` — `@admin(@payload)` → `show @admin(@payload)`

## After Editing

1. Run `npm run build:fixtures` to regenerate fixture JSON files
2. Run `npm run test:case -- exceptions/security` to verify all 8 tests pass
3. Verify the generated fixtures now have non-null ASTs and the expectedError field matches error.md
4. For no-secret-exfil-defaults-override specifically: determine whether the explicit allow should override the built-in rule. If it should succeed, move it out of exceptions/ or update its expected behavior.

## Verification

After fixing, the generated fixture JSON should show:
- `ast` is NOT null (parsed successfully)
- `parseError` is null
- `expectedError` matches the error.md pattern
- The test passes because the policy denial error is thrown at RUNTIME, matching the expected error pattern

