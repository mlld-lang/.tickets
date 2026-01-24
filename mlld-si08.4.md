---
id: mlld-si08.4
status: closed
deps: []
links: []
created: 2026-01-19T22:24:11.26962-08:00
type: task
priority: 1
parent: mlld-si08
---
# Label modification syntax (=>, trusted!, !label, clear!)

## Goal

Implement label modification syntax from spec v4 Part 4.3.

## Syntax

```mlld
// Add labels (anyone can do)
=> sensitive @var
=> pii,internal @var

// Add untrusted (always allowed, replaces trusted)
=> untrusted @var

// Add trusted (warning if already untrusted, both labels kept)
=> trusted @var

// Blessing - remove untrusted (privileged)
=> trusted! @var

// Remove specific labels (privileged)
=> !pii @var
=> !pii,!internal @var

// Clear non-factual labels (privileged)
=> clear! @var
```

## Trust Label Asymmetry

| Operation | Privilege? | Behavior |
|-----------|------------|----------|
| `=> untrusted @var` | No | Replaces `trusted` (taint flows down) |
| `=> trusted @var` | No | Adds `trusted`; if `untrusted` exists, both + warning |
| `=> trusted! @var` | Yes | Blessing: removes `untrusted`, adds `trusted` |
| `=> !label @var` | Yes | Removes specified label |
| `=> clear! @var` | Yes | Removes all non-factual labels |

## trustconflict Behavior

When `=> trusted @var` conflicts with existing `untrusted`:

```mlld
policy {
  defaults: {
    trustconflict: "warn"    // or: "error", "silent"
  }
}
```

- `warn`: Both labels exist, warning logged, treated as untrusted
- `error`: Script fails
- `silent`: Both labels exist, no warning

## Implementation Tasks

1. Grammar updates for return statements with labels
2. Label addition logic
3. Trust asymmetry handling
4. Privilege checking for protected operations
5. `trustconflict` behavior
6. Audit logging for label changes

## Protected Labels (Require Privilege)

- `secret`
- `untrusted` (removal)
- All `src:*` labels

## Exit Criteria

- [ ] `=> label @var` adds labels
- [ ] `=> untrusted @var` replaces trusted
- [ ] `=> trusted @var` warns on conflict
- [ ] `=> trusted! @var` requires privilege
- [ ] `=> !label @var` requires privilege
- [ ] `=> clear! @var` requires privilege
- [ ] `trustconflict` setting works

## References

- Spec v4 Part 4.3: Guard Actions
- Spec v4 Appendix C: Label Modification Syntax

## Estimated Effort

~5 hours



