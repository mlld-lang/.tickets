---
id: m-c1ed
status: closed
deps: []
created: 2026-02-03T03:21:39Z
type: task
priority: 2
assignee: Adam
tags: [urgency-high, phase-3, bug]
updated: 2026-02-03T03:29:50Z
---
# Fix mlld validate crash on security labels

mlld validate fails with 'Cannot use in operator to search for body in untrusted/llm' when parsing 'var untrusted @name' or 'exe llm @name' syntax.

**Root Cause**: In core/utils/dependency-detector.ts line 34, walkAST tries 'body' in node where node is the label string 'untrusted'/'llm' instead of an AST node. Need to check node is an object before using 'in' operator.

**Evidence**: 
- Ran: mlld validate tests/cases/docs/security/41/example.mld
- Error: 'Cannot use in operator to search for body in untrusted'
- Test npm run test:case -- docs/security/41 passes, showing interpreter works but validator has bug

**Impact**: Users cannot validate modules using core security features. The demo had to use mock implementations to remain validatable.

**Fix**: Add type check in walkAST before using 'in' operator:
```typescript
if (node && typeof node === 'object' && 'body' in node) {
```

**Chesterton's Fence**: Labels are stored in a way that confuses the AST walker - it treats the label string as a child node. This is likely unintentional - the walker should skip non-object nodes.


**2026-02-03 03:29 UTC:** Fix verified working. mlld validate now accepts 'var untrusted', 'exe llm' and similar security label syntax. Next: update demo to use full syntax and complete Phase 3 verification criteria.
