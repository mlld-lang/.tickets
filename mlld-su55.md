---
id: mlld-su55
status: open
deps: []
links: []
created: 2026-01-14T20:05:30.921112-08:00
type: task
priority: 2
parent: mlld-vr3w
---
# Track D: Tooling & Polish [LOW]

## Track Overview

Complete the implementation with helper modules, documentation, and publish verification.

**Parent Epic**: mlld-vr3w

## Beads in This Track

### D1: Create helper modules
- @mlld/mcp - MCP configuration utilities
- @mlld/keychain - Keychain wrappers
- @mlld/env - Environment module utilities
- @mlld/env-security - Standard security guards

### D2: Publish verification
- Verify declared /needs matches analyzed /needs
- Warn on mismatch, require confirmation

### D3: Document mlld env commands
- Add to docs/user/cli.md
- Document env module semantics
- Add examples

### D4: env export/import (or defer)
- Either implement export/import commands
- Or document as deferred to v1.1

## Sequence

All items can run in parallel. Lower priority than Tracks A-C.

## Exit Criteria

- [ ] Helper modules published to registry
- [ ] Publish verifies capability declarations
- [ ] User docs complete for mlld env
- [ ] Scope decision made on export/import

## Estimated Effort
12-17 hours total


