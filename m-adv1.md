---
id: m-adv1
status: closed
deps: []
created: 2026-02-04T22:49:29Z
type: task
priority: 2
assignee: Adam
tags: [adversarial, phase-4, env-module]
updated: 2026-02-12T11:30:50Z
---
# Adversarial verification: reusable environment module exit criteria

Verify all exit criteria for the reusable environment module job are met. Exit criteria: (1) Module exports @spawn/@shell, (2) Profile system with full/readonly variants, (3) Credential flow using policy.auth, (4) Import and use from another script. Starting commit: c685e6d66

