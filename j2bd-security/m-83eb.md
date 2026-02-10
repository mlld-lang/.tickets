---
id: m-83eb
status: closed
deps: []
created: 2026-02-09T09:19:26Z
type: task
priority: 2
assignee: Adam Avenir
tags: [phase-3, verification]
updated: 2026-02-09T09:23:55Z
---
# Phase 3: Verify audit guard implementation end-to-end

Run the implementation at llm/run/j2bd/security/impl/main.mld end-to-end and verify each success criterion:

1. Run main.mld and capture output - verify it completes without errors
2. Verify `influenced` label is applied when `untrusted-llms-get-influenced` rule is enabled (check show @influencedResult.mx.labels output)
3. Verify `sign` and `verify` directives work (template signed and verified successfully)
4. Verify `autoverify` policy would inject verification instructions into prompts (check that autoverify: true is in policy config and properly configured)
5. Verify enforcement guard @ensureVerified blocks execution if verify tool not called (test with an llm-labeled exe that doesn't call verify)
6. Test injection attack is detected (the mock auditor should reject content with 'HIDDEN' or 'rm -rf')
7. Verify the when-gated action blocks on failed audit (audit rejected -> action blocked)

Key files:
- llm/run/j2bd/security/impl/main.mld (main implementation)
- llm/run/j2bd/security/impl/prompts/audit-criteria.att (template)

Run with: cd llm/run/j2bd/security/impl && mlld main.mld

For enforcement guard testing, create a small test script that has an llm-labeled exe without MCP verify tool calls and confirm the guard blocks it.

Document all results. If any criterion fails, create friction tickets with specific details.

