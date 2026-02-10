---
id: m-2dd3
status: open
deps: [m-fc63]
created: 2026-02-09T22:01:49Z
type: feature
priority: 0
assignee: Adam Avenir
updated: 2026-02-09T22:01:53Z
tags: [security, testing, llm, dual-audit]
---
# Test dual-audit airlock with real LLM calls

After m-fc63 (user-defined privileged guards) is implemented, re-test the dual-audit airlock demo (llm/run/j2bd/security/impl/main.mld) with real LLM calls instead of mock exes. Current implementation uses plain exe for mock deterministic output; production flow needs exe llm which triggers autoverify and enforcement guards. Verify: (1) both auditor calls actually invoke LLMs, (2) autoverify injects verification instructions, (3) enforcement guard blocks unverified LLM output, (4) privileged user-defined guard can bless tainted data on safe verdict, (5) adversarial injection in tainted context is caught by the information bottleneck. See j2bd/security/jobs/dual-audit-airlock.md for full adversarial test matrix.

