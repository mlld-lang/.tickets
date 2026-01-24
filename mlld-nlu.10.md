---
id: mlld-nlu.10
status: closed
deps: [mlld-nlu.9]
links: []
created: 2025-12-11T22:12:33.16285-08:00
type: task
priority: 2
parent: mlld-nlu
---
# mlld-nlu P9: Acceptance verification

## Acceptance Verification

### Test Scenarios

Run each scenario and verify expected behavior:

#### Scenario 1: Error in Middle
```mlld
/var @before = "works"

/var @broken = INVALID

/var @after = "also works"
```
**Expected:**
- `@before` and `@after` highlighted as variables
- Error squiggle on `INVALID` line
- `/var` keywords highlighted

#### Scenario 2: Error in Block
```mlld
/var @good = 1

/exe @f() = [
  let @x = BROKEN SYNTAX
]

/var @alsoGood = 2
```
**Expected:**
- `@good` and `@alsoGood` highlighted
- Error for the exe block (entire block fails)
- Block `[...]` may or may not be highlighted depending on implementation

#### Scenario 3: Multiple Errors
```mlld
/var @a = 1
BROKEN LINE 1
/var @b = 2
BROKEN LINE 2
/var @c = 3
```
**Expected:**
- `@a`, `@b`, `@c` all highlighted
- Two error squiggles for broken lines

#### Scenario 4: Error at Start
```mlld
BROKEN FIRST LINE
/var @works = "yes"
```
**Expected:**
- `@works` highlighted
- Error on first line

#### Scenario 5: Error at End
```mlld
/var @works = "yes"
BROKEN LAST LINE
```
**Expected:**
- `@works` highlighted
- Error on last line

#### Scenario 6: Strict Mode
```mlld
var @a = 1

BROKEN

var @b = 2
```
**Expected (strict mode):**
- `var` keywords highlighted (not just `/var`)
- `@a` and `@b` highlighted
- Error on BROKEN line

#### Scenario 7: Multi-line Template Preserved
```mlld
/var @msg = `
first line

second line
`

/var @other = "test"
```
**Expected:**
- Template not split (treated as single chunk)
- Both variables highlighted

### Verification Commands
```bash
# Run semantic token tests
npm test services/lsp/semantic-tokens.test.ts

# Check specific error recovery tests
npm test -- --grep "error recovery"

# Manual verification
DEBUG=mlld:lsp mlld language-server
# Then open test files in editor
```

### Acceptance Criteria Checklist
- [ ] Scenario 1: Error in middle - PASS
- [ ] Scenario 2: Error in block - PASS
- [ ] Scenario 3: Multiple errors - PASS
- [ ] Scenario 4: Error at start - PASS
- [ ] Scenario 5: Error at end - PASS
- [ ] Scenario 6: Strict mode - PASS
- [ ] Scenario 7: Multi-line template - PASS
- [ ] All existing LSP tests still pass
- [ ] No performance regression (subjective)

### Deliverable
- [ ] Run all scenarios
- [ ] Document any failures or edge cases
- [ ] Fix issues found during verification
- [ ] Sign off on acceptance criteria


