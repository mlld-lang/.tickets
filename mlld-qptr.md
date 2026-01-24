---
id: mlld-qptr
status: closed
deps: []
links: []
created: 2026-01-19T22:41:52.313522-08:00
type: task
priority: 2
---
# trustconflict behavior (warn/error/silent)

## Summary

Implement trustconflict behavior from spec v4 Section 3.2.

## Feature

When `=> trusted @var` is applied to already-untrusted data:

```mlld
policy {
  defaults: {
    trustconflict: "warn"    // or: "error", "silent"
  }
}
```

## Behavior

| Value | Effect |
|-------|--------|
| `warn` | Both labels exist, warning logged, treated as untrusted |
| `error` | Script fails |
| `silent` | Both labels exist, no warning |

## Audit Integration

Log conflict events to `.mlld/sec/audit.jsonl`:
```jsonl
{"ts": "...", "event": "conflict", "var": "@x", "labels": ["trusted", "untrusted"], "resolved": "untrusted"}
```

## References

- spec-security-2026-v4.md Section 3.2


