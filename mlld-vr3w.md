---
id: mlld-vr3w
status: closed
deps: []
links: []
created: 2026-01-14T20:00:53.62177-08:00
type: task
priority: 0
---
# EPIC: Security Model Remediation (Post-Implementation Review)

## Overview

Post-implementation security review identified critical gaps in the MCP/env/policy implementation. This epic tracks remediation of all identified issues plus the new requirement for explicit capability declarations.

## Review Sources

1. **Claude agents review** - Found MCP arg-level taint gap
2. **gpt52 review** - Found 12 issues across severity levels (see below)

## gpt52 Findings (Full)

### CRITICAL
- **MCP taint never reaches guard evaluation**: Guard inputs are materialized and pre-hooks run BEFORE `meta.mcpSecurity` is merged. `@mx.taint`/`@mx.sources` in guards are empty for MCP calls.
  - References: `exec-invocation.ts:1835,1878,1948`, `FunctionRouter.ts:64`

### HIGH  
- **Keychain `get` returns raw strings without `secret` label**: Resolver only tags the resolver object, not returned values. Guards cannot block exfiltration.
  - References: `KeychainResolver.ts:55,106`, `exec-invocation.ts:1392`

- **Keychain usage not gated by `/needs { keychain }`**: Any script can import/use @keychain without declaring needs.
  - References: `KeychainResolver.ts:55`, `exec-invocation.ts:1392`

- **`mlld env spawn/shell` bypasses module contract**: No module load, no /wants tier resolution, no @mcpConfig, no exported @spawn/@shell call.
  - References: `cli/commands/env.ts:223,253,309`

### MEDIUM
- **`findEnvModule` only checks module.yml existence**: Non-env modules treated as environments.
  - Reference: `cli/commands/env.ts:19`

- **Path traversal in env capture**: `mlld env capture ../x` can write outside `.mlld/env/`.
  - References: `cli/commands/env.ts:19,143`

- **No platform guard for keychain CLI**: Non-macOS errors are opaque spawn errors.
  - References: `cli/commands/env.ts:8,189,281`

- **Guard bypass config gap**: Blocks `guards: false` and `except` but allows `guards: { only: ... }`.
  - Reference: `guard-pre-hook.ts:445`

- **MCP args stringified**: Object/array inputs become `"[object Object]"`.
  - Reference: `FunctionRouter.ts:107`

### LOW
- **env export/import stubbed**: Listed in spec but returns "coming in v1.1".
- **Helper modules missing**: `@mlld/mcp`, `@mlld/keychain`, `@mlld/env`, `@mlld/env-security` not created.
- **User docs missing**: `mlld vars` documented but not `mlld env` commands.

## New Requirement: Explicit Capability Declarations

All importable modules must have `/needs` and `/wants` declarations:
- Makes capability surface visible for opaque modules
- Enables policy decisions before code runs
- `/needs` can be auto-generated via static analysis
- `/wants` allows tiered/optional capabilities

## Parallel Workstreams

| Track | Focus | Beads | Est. Hours |
|-------|-------|-------|------------|
| **A** | Security Model Fixes | 3 | 8-11h |
| **B** | Contract Compliance | 5 | 12-17h |
| **C** | Capability Framework | 4 | 15-23h |
| **D** | Tooling & Polish | 4 | 12-17h |

## Key Documents

- `todo/spec-mcp-env-policy.md` - Original specification
- `todo/plan-mcp-env-policy.md` - Implementation plan
- `todo/audits/*.md` - Pre-implementation audits
- `how-mlld-prevents-consequences-of-injection.md` - Security philosophy

## Exit Criteria

- [ ] All CRITICAL issues resolved
- [ ] All HIGH issues resolved
- [ ] All MEDIUM issues resolved
- [ ] /needs|/wants requirement enforced for imports
- [ ] `mlld analyze` generates capability declarations
- [ ] All tests passing
- [ ] Security model verified end-to-end

## Estimated Total Effort
50-70 hours across all tracks


