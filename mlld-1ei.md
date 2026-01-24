---
id: mlld-1ei
status: closed
deps: []
links: []
created: 2025-12-11T20:51:10.019139-08:00
type: bug
priority: 2
---
# CI: Import error handling test failing (exit code 0 vs 1)

Test fails in CI but passes locally:
- tests/integration/imports/basic-patterns.test.ts > should fail when importing non-existent variable

Error: `expected +0 to be 1 // Object.is equality`

The test expects exitCode 1 when importing a non-existent variable, but CI gets exitCode 0.

## Root Cause
Unknown - timing issue, error handling difference, or environment-specific behavior

## Local Behavior
Test passes locally

## Impact
CI builds fail

## Investigation Needed
- Check error propagation in CI environment
- Verify process.exit() behavior in CI
- May be related to how vitest captures exit codes in CI vs local


