---
id: m-bc82
status: closed
deps: []
created: 2026-02-03T19:39:15Z
type: task
priority: 2
assignee: Adam
tags: [adversarial, phase-4, j2bd-security]
updated: 2026-02-03T19:47:24Z
---
# Adversarial re-verification: prove exit criteria (run 5)

Fresh run requires adversarial verification to prove exit criteria hold.

Prior adversarial results (adversarial-results.md) found:
- PASS: Docker network isolation, Docker filesystem read-only, capabilities.allow, user-defined guards
- FAIL: capabilities.deny, defaults.rules, bare secret show/interpolation
- BLOCKED: env-level tool enforcement (m-3ad1), MCP restrictions (m-289d)

Prior runs updated sandbox-demo.mld to document what IS vs ISN'T enforced.

This ticket must re-verify:
1. Does sandbox-demo.mld run end-to-end without error?
2. Are the documented enforcement claims accurate?
3. Do the working features (Docker isolation, capabilities.allow, guards) still pass?
4. Is the documentation honest about what ISN'T enforced?

The exit criteria states tool restrictions must be PROVEN to block unauthorized tools. If tool restrictions remain unenforced at mlld level (m-3ad1), the decision agent must determine whether Docker-level enforcement satisfies the exit criteria or whether descoping is needed.


**2026-02-03 19:47 UTC:** Run 5 adversarial verification complete. Results: 7/13 PASS, 6/13 FAIL. Key findings: (1) Artifact now runs end-to-end (exit 0) - improvement from prior runs. (2) NEW: capabilities.deny DOES work for cmd:* namespace (prior results were incorrect). (3) CRITICAL NEW: run sh bypasses ALL policy capability controls (both allow and deny). (4) Persistent failures: env block tool enforcement (m-3ad1), secret label default enforcement, defaults.rules. Full results: j2bd/security/runs/2026-02-03-5/worker-m-bc82-1.json
