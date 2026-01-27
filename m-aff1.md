---
id: m-aff1
status: closed
deps: [mlld-964m]
links: []
created: 2026-01-24T04:45:03Z
type: task
priority: 2
assignee: Adam Avenir
parent: mlld-4z2w
---
# Phase 3a-1: External MCP server lifecycle

## Goal

Define and implement lifecycle constraints for spawned external MCP servers.

## Context

mlld-964m enables spawning arbitrary MCP servers but doesn't specify cleanup. Risk: orphaned processes, unbounded network access, resource exhaustion.

## Requirements

### Lifecycle Constraints

| Constraint | Default | Configurable |
|------------|---------|--------------|
| Startup timeout | 10s | Yes |
| Idle timeout | 60s | Yes |
| Max concurrent servers | 5 | Yes |
| Kill on session end | Yes | No |
| Kill on env block exit | Yes | No |

### Implementation

1. **Startup timeout**: Server must respond to `initialize` within timeout
2. **Idle timeout**: No calls for N seconds → graceful shutdown
3. **Max concurrent**: Queue or reject if limit hit
4. **Session cleanup**: Track PIDs, SIGTERM on exit, SIGKILL after grace period
5. **Orphan prevention**: PID file in `.mlld/run/mcp-<id>.pid`

### Error Handling

- Startup failure → error with stderr capture
- Crash during call → error, mark server unhealthy, retry spawn once
- Timeout during call → error, don't kill server (may be slow operation)

## Exit Criteria

- [ ] Startup timeout enforced
- [ ] Idle timeout triggers graceful shutdown
- [ ] Max concurrent limit enforced
- [ ] All servers killed on session/block exit
- [ ] No orphaned processes after crash scenarios
- [ ] Test: spawn, timeout, cleanup sequence

## Estimated Effort
~4 hours

## Depends On
mlld-964m (external MCP spawning)

