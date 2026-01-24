---
id: mlld-wmzl.15
status: closed
deps: []
links: []
created: 2026-01-16T15:08:24.442573-08:00
type: task
priority: 1
parent: mlld-wmzl
---
# Implement defaults object (v4 design complete)

## Status

**DESIGN COMPLETE** - See `todo/spec-security-2026-v4.md`

## Summary

The v4 spec finalizes the design for how unlabeled data is handled and how the `defaults` object works.

## v4 Design Decisions

### Q1: Labels without rules are ALLOWED

```mlld
policy {
  defaults: { unlabeled: untrusted },
  labels: {
    "src:mcp": { allow: [op:cmd:git:status] }
    // No rule for "secret" label
  }
}

var secret @token = keychain.get(...)
show @token  // ALLOWED - labels without explicit rules are unrestricted
```

Rationale: Otherwise you'd need rules for every possible label.

### Q2: Most restrictive wins on merge

When policies compose, `unlabeled: untrusted` beats `unlabeled: trusted`.

### Q3: Core defense via opt-in rules

The old `default:deny` prefix syntax is replaced by the `defaults` object:

```mlld
policy {
  defaults: {
    unlabeled: untrusted,              // Trust stance for unlabeled data
    rules: [
      "no-secret-exfil",
      "no-sensitive-exfil",
      "no-untrusted-destructive",
      "no-untrusted-privileged",
      "untrusted-llms-get-influenced"
    ],
    autosign: ["templates"],
    autoverify: true,
    trustconflict: "warn"
  }
}
```

### "Unlabeled" definition

"Unlabeled" = no user-applied labels (but may have `src:*` markers). The `src:*` markers are provenance tracking, not "labels" in the policy sense.

## Implementation Tasks

1. Parse `defaults` object in policy
2. Implement `unlabeled` trust stance enforcement
3. Implement opt-in `rules` enforcement
4. Implement `trustconflict` behavior
5. Integrate with `autosign`/`autoverify` (separate beads)

## Exit Criteria

- [ ] `defaults.unlabeled` parsed and enforced
- [ ] `defaults.rules` enables built-in security rules
- [ ] `defaults.trustconflict` controls conflict behavior
- [ ] Tests verify all `defaults` options

## References

- Spec v4 Part 3.2: The `defaults` Object
- Spec v4 Part 3.4: Label Flow Rules

## Estimated Effort

~4 hours



