---
id: m-fa3f
status: closed
deps: []
created: 2026-02-03T05:42:20Z
type: task
priority: 2
assignee: Adam
tags: [phase-1, doc]
updated: 2026-02-03T05:46:32Z
---
# Atom: policy-capabilities - restricting tools, fs, network

Create policy-capabilities.md atom in docs/src/atoms/security/ covering:

1. Capability syntax in policy.capabilities object
2. Tool restrictions via allow/deny lists (cmd:git:*, cmd:npm:*, etc.)
3. Filesystem restrictions via fs:r and fs:w patterns
4. Network restrictions
5. The danger list for operations requiring explicit opt-in

Reference spec Part 3.3 (Capability Syntax) for authoritative syntax.

Include working mlld examples that pass `mlld validate`. Target 100-200 words.

Existing policies.md is a minimal stub and should NOT be replaced - this is a new focused atom on the capabilities subsystem specifically.

