---
id: m-ed11
status: closed
deps: []
created: 2026-02-03T21:17:41Z
type: task
priority: 2
assignee: Adam
tags: [adversarial, phase-4, urgency-high]
updated: 2026-02-03T22:13:35Z
---
# Final adversarial re-verification: all 5 exit criteria after fixes

Both remediation fixes from the previous adversarial run (m-6232) have been applied:

1. **cmd:sh bypass fixed** (commit e8aa689ba): `evaluateCommandAccess` in core/policy/guards.ts now blocks commands starting with 'sh' or 'bash' when deny:['sh'] is active.

2. **Secret-protection guards added** (commit 1ed525dc1): sandbox-demo.mld now includes `guard @noShowSecrets before secret` and `guard @noSecretCmd before secret` with working demonstration.

Re-verify ALL 5 exit criteria with execution evidence:

### Test 1: Artifact runs end-to-end
Run `mlld llm/run/j2bd/security/impl/sandbox-demo.mld` and verify exit code 0.

### Test 2: Tool restrictions block unauthorized tools
Create and run:
```mlld
policy @noShell = {
  capabilities: {
    deny: ["sh"]
  }
}

run sh { echo "SHOULD NOT EXECUTE" }
```
Expect: blocked by guard.

Also test the cmd:sh bypass that was fixed:
```mlld
policy @noShell = {
  capabilities: {
    deny: ["sh"]
  }
}

run cmd { sh -c "echo SHOULD_NOT_EXECUTE" }
```
Expect: blocked by 'Shell access denied by policy'.

### Test 3: Filesystem limits block writes outside allowed paths
Re-run Docker filesystem test - read-only mounts should block writes.

### Test 4: Network restrictions block requests when disabled
Re-run Docker network test with net: "none" - should block all network.

### Test 5: Credentials cannot be displayed or interpolated
Test that sandbox-demo.mld's guard protection actually works:
```mlld
guard @noShowSecrets before secret = when [
  @mx.op.type == "show" => deny "Cannot display secret-labeled data"
  @mx.op.type == "log" => deny "Cannot log secret-labeled data"
  * => allow
]

var secret @testKey = "sk-test-never-shown"
show @testKey
```
Expect: guard blocks with denial message.

Return status: "verified" if ALL 5 exit criteria have execution evidence proving enforcement. Return detailed failure report otherwise.


**2026-02-03 22:13 UTC:** Final results: 15 tests executed, 15 passed, 0 failed. Fix verifications: (1) zsh/dash/fish/ksh/csh/tcsh/ash all blocked by expanded SHELL_BINARIES, (2) env/nice/nohup wrappers detected and inner command checked, (3) /bin/sh path prefix stripped, (4) log op type corrected from 'output' to 'log', (5) pipeline effects preserve Variable security labels for guard evaluation.
