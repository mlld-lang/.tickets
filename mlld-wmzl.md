---
id: mlld-wmzl
status: closed
deps: []
links: []
created: 2026-01-16 07:17:05 -0800
type: epic
priority: 0
tags: [size-xl, complexity-xl, risk-xl]
---
# Security Model v3: Label Flow Implementation

## Overview

Implement mlld's security model for AI agent orchestration. The core insight:

> **You cannot prevent LLMs from being tricked by prompt injection. But you CAN prevent the consequences of being tricked from manifesting.**

This epic implements the label flow system that tracks data provenance and prevents tainted data from reaching dangerous operations.

## Key Documents

- **Spec**: `todo/spec-security-2026-v3.md` - Complete specification
- **Plan**: `todo/plan-security-2026-v3.md` - Implementation phases with code examples
- **Branch**: `envsec`

## Architecture Summary

```
┌─────────────────────────────────────────────────────────────────────┐
│  LLM Decision Space (UNSECURABLE)                                   │
│  - Can be influenced by any input (prompt injection)                │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼ Every operation
┌─────────────────────────────────────────────────────────────────────┐
│  mlld Execution Layer (SECURABLE)                                   │
│  - Labels track what data IS and where it CAME FROM                 │
│  - Policy declares what CAN happen                                  │
│  - Guards enforce with full context                                 │
└─────────────────────────────────────────────────────────────────────┘
```

## What Works (Already Implemented)

| Component | Location |
|-----------|----------|
| SecurityDescriptor type | `core/types/security.ts` |
| Label propagation | `taint-post-hook.ts` |
| Basic policy composition | `core/policy/union.ts` |
| Guard system | `interpreter/hooks/` |
| Keychain + `secret` label | `KeychainResolver.ts` |
| `src:mcp` taint | `FunctionRouter.ts` |
| Environment CLI (partial) | `cli/commands/env.ts` |

## Critical Gaps (This Epic)

| Gap | Impact |
|-----|--------|
| `PolicyConfig.labels` | Can't define label flow rules |
| Label flow checking | Core defense missing |
| `allowEnvTo` | Auth token path blocked |
| `op:*` auto-generation | Operations unlabeled |
| Prefix matching | Hierarchy doesn't work |
| Most-specific-wins | Can't have exceptions |

## Phases

### Phase 1 (P0): Remove `untrusted` auto-labeling
Fix spec violations - `untrusted` should NEVER be auto-applied.

### Phase 2 (P0): PolicyConfig.labels type
Add `labels` section to PolicyConfig for label flow rules.

### Phase 3 (P0): op:* auto-applied labels  
Operations get hierarchical labels like `op:cmd:git:status`.

### Phase 4 (P0): Label flow checker
Core defense - check if labeled data can flow to operations.

### Phase 5 (P1): src:* source labels
Auto-apply provenance labels at data entry points.

### Phase 6 (P1): Guard label modification
Guards can add/remove labels via `allow with { removeLabels: [...] }`.

### Phase 7 (P1): /profiles directive
Replace /wants with /profiles for graceful degradation.

### Phase 8 (P1): MCP server lifecycle
Actually use @mcpConfig() to spawn MCP servers.

### Phase 9 (P2): Jails
Docker isolation via guard action.

## Exit Criteria

**P0 Complete** (core defense functional):
- [ ] `untrusted` never auto-applied
- [ ] `PolicyConfig` has `labels` section
- [ ] `op:*` labels auto-generated for all operations
- [ ] Label flow checking with prefix matching
- [ ] Most-specific-wins resolution
- [ ] `allowEnvTo` functional
- [ ] Tests verify label flow blocks tainted data

**P1 Complete** (full environment/MCP):
- [ ] `src:*` labels auto-applied at entry points
- [ ] Guards can add/remove labels
- [ ] `/profiles` directive works
- [ ] MCP server lifecycle orchestrated

**P2 Complete** (isolation):
- [ ] `jail` guard action works
- [ ] Docker isolation functional

## Estimated Effort

- P0: ~15 hours
- P1: ~15 hours  
- P2: ~8 hours
- Total: ~38 hours


