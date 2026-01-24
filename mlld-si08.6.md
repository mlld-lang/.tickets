---
id: mlld-si08.6
status: open
deps: []
links: []
created: 2026-01-19T22:24:48.342433-08:00
type: task
priority: 2
parent: mlld-si08
---
# Audit ledger (.mlld/sec/audit.jsonl)

## Goal

Implement the audit ledger from spec v4 Part 15.

## Location

```
.mlld/sec/
  audit.jsonl       # Append-only audit log
  sigs/             # Signature storage
```

## Events Logged

### Signing events
```jsonl
{"ts": "...", "event": "sign", "var": "@auditPrompt", "hash": "sha256:abc", "by": "alice"}
```

### Verification events
```jsonl
{"ts": "...", "event": "verify", "var": "@auditPrompt", "result": true, "caller": "exe:audit"}
```

### Label events
```jsonl
{"ts": "...", "event": "label", "var": "@data", "add": ["sensitive"], "by": "guard:classify"}
```

### Blessing events
```jsonl
{"ts": "...", "event": "bless", "var": "@out", "remove": ["untrusted"], "add": ["trusted"], "by": "guard:validate"}
```

### Trust conflict events
```jsonl
{"ts": "...", "event": "conflict", "var": "@x", "labels": ["trusted", "untrusted"], "resolved": "untrusted"}
```

### File write events (taint tracking)
```jsonl
{"ts": "...", "event": "write", "path": "/tmp/out.json", "taint": ["src:mcp", "untrusted"], "writer": "mcp:github:list_issues"}
```

## Query API

### CLI
```bash
mlld audit why @data            # Provenance chain for labels
mlld audit history @data        # All events for variable
mlld audit signed               # List all signed variables
mlld audit blessed              # List all blessing events
```

### Programmatic
```mlld
@mlld.audit.why(@var)           // Returns provenance chain
@mlld.audit.history(@var)       // Returns all events
@mlld.audit.signed()            // Returns signed variables
@mlld.audit.blessed()           // Returns blessing events
```

## File Taint Tracking

When a file is written:
- Record path, writer, and taint state
- On subsequent read, inherit recorded taint

## Implementation Tasks

1. Create `.mlld/sec/` directory structure
2. Append-only JSONL logging
3. Event types and schemas
4. CLI query commands
5. Programmatic query API
6. File taint tracking integration

## Log Retention

Left to external tooling (logrotate, etc.). File is append-only JSONL, compatible with standard log tools.

## Exit Criteria

- [ ] All event types logged correctly
- [ ] `mlld audit why @var` works
- [ ] `mlld audit history @var` works
- [ ] File write events tracked
- [ ] File read inherits recorded taint

## References

- Spec v4 Part 15: Audit Ledger

## Estimated Effort

~6 hours



