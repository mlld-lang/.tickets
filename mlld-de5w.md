---
id: mlld-de5w
status: closed
deps: []
links: []
created: 2026-01-15T11:04:55.868437-08:00
type: bug
priority: 2
parent: mlld-urik
---
# MEDIUM: /needs enforcement disabled in tests

## Problem

/needs enforcement is disabled by default in tests. When enabled, many test cases fail.

## Current State

From gpt52:
- `tests/setup.ts:26` - Sets flag to disable needs gating
- Many tests don't declare /needs but use capabilities
- Enforcement is spec-critical but remains unverified

## Impact

- Tests pass with enforcement OFF
- Production has enforcement ON
- Behavior differs between test and production
- Can't verify /needs enforcement actually works

## Required Fix

### 1. Enable enforcement in tests by default

```typescript
// tests/setup.ts
// Remove or flip the disable flag
process.env.MLLD_DISABLE_NEEDS_GUARD = 'false';
```

### 2. Update failing tests to declare /needs

Each test that uses capabilities must declare them:
```mlld
/needs { sh }
/run echo "test"
```

Or for tests that intentionally test without needs:
```mlld
>> @test-config: { disableNeedsGating: true }
/run echo "test"
```

### 3. Create test categories

- `tests/cases/needs-enforced/` - Tests WITH enforcement
- `tests/cases/needs-disabled/` - Tests that intentionally bypass

## Scope Question

> Should /needs enforcement apply to all imported modules or only environment modules?

Recommendation: **All imported modules** should declare /needs. This is the principle we established - capability contracts must be explicit.

Local scripts (not imported) can omit /needs for convenience.

## Exit Criteria

- [ ] Tests run with /needs enforcement enabled
- [ ] Failing tests updated with proper /needs declarations
- [ ] Clear separation of enforced vs intentionally-disabled tests
- [ ] CI verifies enforcement works

## Estimated Effort
4-6 hours (mostly updating test fixtures)


