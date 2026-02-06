---
id: m-2ac4
status: open
deps: []
links: []
created: 2026-01-28T23:46:02Z
type: task
priority: 1
parent: mlld-vgx2
assignee: Adam
tags: [docs, doc-fix, security, howto, impl-done, needs-human-design]
updated: 2026-01-30T16:23:35Z
---
# Create security howto atoms

Create atomic documentation files for the security model that work with the howto pattern (see docs/dev/HOWTO-PATTERN.md).

## Goal

Systematically atomize all key security concepts from spec-security-2026-v4.md into `docs/src/atoms/security/` so that:
- `mlld howto security` shows the topic tree
- `mlld howto security labels` shows label documentation
- `mlld howto security guards` shows guard documentation
- etc.

## Requires Design

Need to determine:
1. **Atom structure** - How to break down the v4 spec into self-contained atoms
2. **Progressive disclosure** - What's the learning path? (labels → guards → policies? or different?)
3. **What to include** - Which v4 concepts are implemented vs planned?
4. **Parent ticket mlld-vgx2** has the full list of concepts from the spec

## Notes

**2026-01-30 15:16 UTC:** Enrich reviewed: 2026-01-30T15:14:56.295Z

**2026-01-30 16:23 UTC:** Reverted commit 3120107. The overview doc only covered ~10-15% of the spec (labels 101). Missing: source label tracking (core of prompt injection defense), credential flow (policy.auth + using auth:*), trust label mechanics, signing/verification, environments, privileged guards, MCP integration, audit ledger. Needs proper design that reflects the full security model for AI agent orchestration per todo/spec-security-2026-v4.md.
