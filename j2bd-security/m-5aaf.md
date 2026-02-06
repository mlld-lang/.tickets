---
id: m-5aaf
status: closed
deps: []
created: 2026-02-05T12:46:54Z
type: task
priority: 2
assignee: Adam
tags: [phase-3, verification]
updated: 2026-02-05T12:49:12Z
---
# Verify builtin-rules enforcement

Phase 3 verification: Run builtin-rules-demo.mld and confirm exit criteria.

**Verification checklist:**
1. Run `mlld run j2bd/security/artifacts/builtin-rules-demo.mld` - should fail with policy denial
2. Confirm error message mentions `no-secret-exfil` rule name
3. Modify script to test `@deleteFile(@userInput)` - should fail with `no-untrusted-destructive`
4. Confirm error message mentions `no-untrusted-destructive` rule name

**Expected outcomes:**
- Secret data blocked from exfil operations
- Untrusted data blocked from destructive operations
- Error messages clearly indicate which rule caused the denial

**If tests fail:**
- Document exact error messages received
- Note any gaps between expected and actual behavior
- Create follow-up tickets for implementation fixes needed


**2026-02-05 12:48 UTC:** ## Verification Complete

All builtin rules verified working correctly:

### 1. no-secret-exfil ✓
- **Command**: `npm run mlld -- j2bd/security/artifacts/builtin-rules-demo.mld`
- **Result**: Policy denial at line 131 (secret @apiKey → exfil @sendToServer)
- **Error includes rule**: `policy.defaults.rules.no-secret-exfil`
- **Reason**: "Label 'secret' cannot flow to 'exfil'"

### 2. no-untrusted-destructive ✓
- **Command**: `npm run mlld -- tmp/test-untrusted-destructive.mld`  
- **Result**: Policy denial (untrusted @userInput → destructive @deleteFile)
- **Error includes rule**: `policy.defaults.rules.no-untrusted-destructive`
- **Reason**: "Label 'untrusted' cannot flow to 'destructive'"

### 3. no-untrusted-privileged ✓
- **Command**: `npm run mlld -- tmp/test-untrusted-privileged.mld`
- **Result**: Policy denial (untrusted @userInput → privileged @modifyConfig)
- **Error includes rule**: `policy.defaults.rules.no-untrusted-privileged`
- **Reason**: "Label 'untrusted' cannot flow to 'privileged'"

### Error Message Quality
All error messages include:
- Rule name in full qualified form (policy.defaults.rules.no-*)
- Input and operation labels for debugging
- Clear reason statement
- Actionable suggestions for remediation
