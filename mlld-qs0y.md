---
id: mlld-qs0y
status: closed
deps: []
links: []
created: 2026-01-14T16:00:45.659191-08:00
type: task
priority: 0
---
# EPIC: MCP, Environment Modules, and Policy Integration

## Overview

This epic implements secure AI agent orchestration through environment modules, MCP integration, and policy-based security enforcement.

**The Core Insight**: You cannot prevent LLMs from being tricked by prompt injection. But you CAN prevent the consequences by enforcing security at the execution layer.

## Key Documents

### Specifications
- `todo/spec-mcp-env-policy.md` - Unified specification (1117 lines)
- `todo/plan-mcp-env-policy.md` - Implementation plan with phases

### Audits (Current State Analysis)
- `todo/audits/audit-policy.md` - Policy system gaps
- `todo/audits/audit-needs-wants.md` - Needs/wants gaps  
- `todo/audits/audit-guards.md` - Guard system gaps
- `todo/audits/audit-mcp.md` - MCP system gaps
- `todo/audits/audit-cli-env.md` - CLI env command analysis

### Vision
- `how-mlld-prevents-consequences-of-injection.md` - Security philosophy

### Testing
- `docs/dev/TESTS.md` - Test structure and conventions

## Security Model (Three Dimensions)

```
@mx.labels  - What it IS (secret, pii, untrusted)
@mx.taint   - Where it CAME FROM (src:mcp, src:exec, src:file)
@mx.sources - HOW it got here (mcp:createIssue, guard:@sanitize)
```

## Workstreams

Four parallel workstreams for maximum efficiency:

| Stream | Focus | Key Beads |
|--------|-------|-----------|
| A | CLI Infrastructure | 1.1 → 6.0 (rename, router) |
| B | Taint & MCP Security | 1.2 → 3.1 (taint exposure, MCP tracking) |
| C | Policy Enforcement | 2.1 → 2.5 → 9 (tier selection, guards, bypass config) |
| D | Keychain & Capabilities | 2.2 → 2.3 → 4.x (grammar, validation, implementation) |

## Critical Security Items

These MUST complete for security model to work:
- [ ] mlld-yb8a: Expose @mx.taint to guards
- [ ] mlld-7ud1: Fix tier selection to respect policy
- [ ] mlld-14od: Policy guard generation + privileged guards
- [ ] mlld-atyp: Complete MCP security tracking
- [ ] mlld-8ohj: Validate /needs at load time
- [ ] mlld-e817: Guard bypass configuration

## Architecture Summary

```
┌─────────────────────────────────────────────────────────┐
│  LLM Decision Space (UNSECURABLE)                       │
│  - Can be influenced by prompt injection                │
│  - Outputs: MCP tool calls                              │
└──────────────────────────┬──────────────────────────────┘
                           │
                           ▼ Every tool call
┌─────────────────────────────────────────────────────────┐
│  mlld Execution Layer (SECURABLE)                       │
│  - Guards intercept every operation                     │
│  - Taint tracks data provenance                         │
│  - Policy constrains capabilities                       │
│  - Labels propagate through transformations             │
└─────────────────────────────────────────────────────────┘
```

## Exit Criteria (Complete Epic)

- [ ] All security-critical beads complete
- [ ] `mlld env capture/list/spawn/shell` working
- [ ] Keychain credential storage functional (macOS)
- [ ] Policy guards enforce allow/deny rules
- [ ] MCP tool calls carry proper taint
- [ ] All tests passing per docs/dev/TESTS.md
- [ ] Documentation updated

## Estimated Total Effort
~83.5 hours across all phases
~25-30 hours with 4 parallel agents


