---
id: m-ca07
status: closed
deps: []
created: 2026-02-04T22:49:41Z
type: task
priority: 2
assignee: Adam
tags: [adversarial, phase-4, env-module]
updated: 2026-02-04T22:59:15Z
---
# Adversarial verification: reusable environment module exit criteria

## Purpose
Verify all exit criteria for the reusable environment module job are met.

## Exit Criteria to Verify

1. **Module exports @spawn and @shell** - Check pipeline/env-module/index.mld exports these
2. **Profile system with full/readonly variants** - Verify profiles {} syntax works
3. **Credential flow using policy.auth** - Verify policy.auth config and using auth:name work
4. **Import and use from another script** - Verify pipeline/env-module-demo.mld works
5. **User can create a packaged environment module** - End-to-end workflow

## Verification Approach

1. Run `mlld validate` on both module and demo files
2. Verify profiles {} directive parses and functions (may need to check interpreter behavior)
3. Verify policy {} with auth block parses correctly
4. Verify `using auth:name` syntax is supported
5. Attempt to break claims - try to use credentials without using auth:, try to bypass profiles

## Starting Commit
c685e6d66

## Expected Outcome
All exit criteria pass with evidence. If gaps found, document them for remediation.


**2026-02-04 22:59 UTC:** Worker verified: module exports work, profile selection works with policy restrictions, credential flow via policy.auth and using auth:name work, import/use from other scripts work. Direct keychain access properly blocked. Undefined auth configs produce clear errors.
