---
id: mlld-v6pv
status: open
deps: []
links: []
created: 2026-01-14T20:06:42.556805-08:00
type: task
priority: 3
parent: mlld-su55
---
# D4: env export/import commands (scope decision)

## Summary

Decide scope for `mlld env export` and `mlld env import` commands.

## Current State

Commands are stubbed with "coming in v1.1" message.

## Options

### Option A: Implement Now

```bash
# Export environment template (without secrets)
mlld env export my-claude > template.mlldenv

# Import on another machine
mlld env import template.mlldenv my-claude
# Prompts for credentials
```

Implementation:
- Export serializes module files (not credentials)
- Guards block secret-labeled values from export
- Import creates module, prompts for credentials
- Estimate: 4-6 hours

### Option B: Defer to v1.1

- Keep current stub
- Document as planned feature
- Focus on core security model first
- Estimate: 1 hour (just documentation)

## Recommendation

**Defer to v1.1** because:
1. Core security model (Tracks A-C) is higher priority
2. Manual copy of module files works as workaround
3. Keychain credentials are machine-specific anyway

## Exit Criteria

If deferring:
- [ ] Update stub message with rationale
- [ ] Document workaround (manual file copy + credential setup)
- [ ] Add to v1.1 roadmap

If implementing:
- [ ] export command works
- [ ] import command works
- [ ] Secrets blocked from export
- [ ] Tests verify both commands

## Estimated Effort
1 hour (defer) or 4-6 hours (implement)


