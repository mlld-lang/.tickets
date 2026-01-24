---
id: mlld-jw7
status: closed
deps: [mlld-9id, mlld-0ic, mlld-ait]
links: []
created: 2025-12-09T22:19:25.090487-08:00
type: task
priority: 1
---
# Phase 3: Core Tests - Minimal golden set for block syntax epic

## Summary

Create 5 core test cases that validate each feature works in isolation. These are the minimal golden set before moving to integration tests.

## Prereq

All Phase 2 interpreter issues must be complete:
- mlld-zeo (Export let functions)
- mlld-9id (Exe blocks)
- mlld-0ic (For blocks)
- mlld-ait (While loops)

## Test Structure

```
tests/cases/feat/
├── exe-block/
│   └── basic/
│       ├── example.md
│       └── expected.md
├── for-block/
│   └── basic/
│       ├── example.md
│       └── expected.md
├── when-unified/
│   └── basic/
│       ├── example.md
│       └── expected.md
├── while/
│   └── basic-done-continue/
│       ├── example.md
│       └── expected.md
tests/cases/exceptions/
└── augmented-assignment/
    └── undefined-variable/
        ├── example.md
        └── error.md
```

## Test 1: Exe Block Happy Path

File: `tests/cases/feat/exe-block/basic/example.md`
```mlld
/exe @greet(name) = [
  let @greeting = "Hello"
  let @punctuation = "!"
  => "@greeting @name@punctuation"
]

/show @greet("World")
```

Expected: `expected.md`
```
Hello World!
```

**Purpose**: Exe block with let assignments and explicit return.

---

## Test 2: For Block Happy Path (UPDATED)

File: `tests/cases/feat/for-block/basic/example.md`
```mlld
/exe @countAndShow(items) = [
  let @count = 0
  for @item in @items [
    show "Item: @item"
    let @count += 1
  ]
  => @count
]

/show "Total: @countAndShow(["a", "b", "c"])"
```

Expected: `expected.md`
```
Item: a
Item: b
Item: c
Total: 3
```

**Purpose**: For block with let/let+= for accumulation (wrapped in exe block since let is block-scoped).

**Note**: `var +=` is NOT supported. Use `let` for accumulation within exe blocks.

---

## Test 3: When Unified Syntax

File: `tests/cases/feat/when-unified/basic/example.md`
```mlld
/exe @classify(x) = when [
  @x > 100 => "large"
  @x > 10 => "medium"
  * => "small"
]

/show @classify(150)
/show @classify(50)
/show @classify(5)
```

Expected: `expected.md`
```
large
medium
small
```

**Purpose**: When expression with unified `[condition => action]` syntax.

---

## Test 4: While Loop Happy Path

File: `tests/cases/feat/while/basic-done-continue/example.md`
```mlld
/exe @countdown(n) = when [
  @n <= 0 => done "finished"
  * => continue (@n - 1)
]

/var @result = 5 | while(10) @countdown
/show @result
```

Expected: `expected.md`
```
finished
```

**Purpose**: While loop with done/continue keywords.

---

## Test 5: Error - Augmented Assignment Without Let

File: `tests/cases/exceptions/augmented-assignment/undefined-variable/example.md`
```mlld
/exe @bad() = [
  let @count += 1
]
/show @bad()
```

Expected: `error.md`
```
Cannot use += on undefined variable @count. Use "let @count = ..." first.
```

**Purpose**: Runtime error for += without prior let.

---

## Running Tests

```bash
# Build fixtures first
npm run build:fixtures

# Run all block syntax tests
npm run test:case -- feat/exe-block
npm run test:case -- feat/for-block
npm run test:case -- feat/when-unified
npm run test:case -- feat/while

# Run exception tests
npm run test:case -- exceptions/augmented-assignment
```

## Validation

- [ ] All 5 test cases created
- [ ] Fixtures generated without errors
- [ ] All tests pass
- [ ] Edge cases identified for Phase 4

## Notes

✅ PREREQUISITE UPDATE: Phase 1 AST verification (mlld-b4f) complete. All grammar constructs parse correctly. Type enums updated. Test Results: 2391/2391 passed (100%). All AST structures validated and documented in tmp/phase1-ast-verification.md. Ready to implement Phase 3 core tests once Phase 2 interpreters are complete.


