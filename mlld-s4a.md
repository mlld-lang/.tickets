---
id: mlld-s4a
status: open
deps: []
links: []
created: 2025-12-10T22:57:01.302139-08:00
type: bug
priority: 2
---
# npm run test:case shows all tests skipped instead of running specified test

**Repro:**
```bash
npm run test:case -- feat/for-when-filter
```

**Expected:** Runs the specific test fixture

**Actual:**
```
Test Files  1 skipped (1)
Tests  1012 skipped (1012)
```

All tests show as skipped instead of running the specified test. This broke recently - the test:case runner should isolate and run only the matching fixture(s).

**Impact:** Can't quickly test individual fixtures during development.


