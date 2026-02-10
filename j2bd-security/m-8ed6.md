---
id: m-8ed6
status: closed
deps: []
created: 2026-02-09T18:48:52Z
type: task
priority: 2
assignee: Adam Avenir
tags: [phase-3, verification]
updated: 2026-02-09T19:01:20Z
---
# Phase 3: Verify sandbox demo and atom examples end-to-end

Run the existing sandbox-demo.mld end-to-end and verify all atom examples.

## Verification checklist:

1. Run `mlld llm/run/j2bd/security/impl/sandbox-demo.mld` - does it complete without error?
2. Verify `env @config [ ... ]` block syntax works (lines 117-124 of sandbox-demo.mld)
3. Verify tool restrictions are configured correctly (tools: ['Read', 'Write'])
4. Verify filesystem limits are configured (fs.read, fs.write)
5. Verify network limits are configured (net: 'none')
6. Verify credential injection pattern works (using auth:name)
7. Verify secret protection guards fire correctly (guard @noShowSecrets, @noSecretCmd)
8. Validate all code examples in the 5 atoms pass `mlld validate`:
   - docs/src/atoms/security/env-overview.md
   - docs/src/atoms/security/env-config.md
   - docs/src/atoms/security/env-blocks.md
   - docs/src/atoms/security/policy-auth.md
   - docs/src/atoms/security/policy-capabilities.md

## File locations:
- Sandbox demo: llm/run/j2bd/security/impl/sandbox-demo.mld
- Atoms: docs/src/atoms/security/

## Report:
For each item, report PASS/FAIL with actual output. If any FAIL, identify the root cause and whether it's a doc issue, implementation issue, or mlld gap.

