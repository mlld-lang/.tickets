---
id: mlld-x93u
status: open
deps: []
links: []
created: 2026-01-18T11:29:29.792929-08:00
type: task
priority: 3
---
# Detect and wrap existing MCP configs during env capture

## Summary

When capturing a Claude Code environment, detect existing MCP server configs and optionally wrap them as mlld-native MCPs.

## Context

mlld intends to be the MCP control layer for complete taint tracking. Rather than copying MCP configs directly, we should:

1. **Detect** what MCPs are configured in the source environment
2. **If node module:** Import and wrap as mlld-native MCP
3. **If CLI-based:** Wrap via mlld MCP proxy
4. **Generate** mlld-native MCP config in the captured environment

## Benefits

- Complete taint tracking (all MCP data flows through mlld)
- Policy enforcement on MCP calls
- No credential leakage from MCP configs

## Implementation Notes

- Claude Code MCP configs location: TBD (investigate)
- mlld already has `mlld serve` for MCP proxy capability
- Would generate `@mcpConfig()` in captured environment module

## Status

Maybe/Later - Enhancement to env capture. Core capture works without this.

## References

- mlld-env-proposal.md
- cli/commands/env.ts (capture function)


