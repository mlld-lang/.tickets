---
id: m-b7cf
status: closed
deps: []
created: 2026-02-03T05:27:47Z
type: task
priority: 2
assignee: Adam
tags: [phase-1, doc]
updated: 2026-02-03T05:32:11Z
---
# Atom: env-overview - environments and security role

Write the env-overview atom explaining:
- What environments are in mlld (execution contexts with credentials, isolation, capabilities)
- Why they matter for security (contain agent capabilities, limit blast radius)
- The core concept that environments are values (can be computed, composed, passed)
- Brief mention of provider options (docker, sprites, or no provider for local)

Target: 100-200 words with working mlld example showing basic environment definition.

Reference: Part 5 of spec (Environments as unifying primitive)


**2026-02-03 05:32 UTC:** Worker result confirmed: both mlld examples validated successfully.
