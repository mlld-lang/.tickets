---
id: m-5af3
status: open
deps: []
created: 2026-02-05T01:21:20Z
type: task
priority: 1
assignee: Adam
tags: [adversarial, urgency-high]
updated: 2026-02-05T01:21:23Z
---
# Adversarial verification of sandbox restrictions

## Context

All 5 atoms exist (env-overview, env-config, env-blocks, policy-auth, policy-capabilities) and sandbox-demo.mld runs. Need to PROVE each exit criterion with execution evidence.

## Exit Criteria to Verify

1. **Artifact runs** - sandbox-demo.mld executes without error
2. **Tool restrictions** - Code inside env block with tools:[Read,Write] cannot use other tools
3. **Filesystem limits** - Write to path outside fs.write patterns is blocked
4. **Network restrictions** - Request with net:none is blocked
5. **Credential protection** - Secret-labeled data cannot be showed or interpolated into commands

## Code Evidence Found (for targeting tests)

- Tool restrictions: setAllowedTools in env.ts:144, checked via isToolAllowed in MCPServer.ts:142
- Filesystem: enforceFilesystemAccess called from output.ts:733, append.ts:111
- Network: NO enforcement code found for net:none - documented API only
- Credentials: Guard-based protection demonstrated in sandbox-demo.mld

## Test Approach

For each criterion, create mlld code that ATTEMPTS the restricted action and verify it FAILS with expected error.

## Expected Outcome

- Criteria 1,2,3,5: Should pass with execution evidence
- Criterion 4 (network): Likely to fail - need to document gap or find enforcement

## Deliverables

For each test:
1. The exact mlld code run
2. Expected behavior (should be blocked)
3. Actual output (proving blocked or revealing gap)

