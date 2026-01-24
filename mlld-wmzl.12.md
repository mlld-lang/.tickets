---
id: mlld-wmzl.12
status: open
deps: [mlld-wmzl.4, mlld-dpgy, mlld-wmzl.15, mlld-si08.2, mlld-si08.3, mlld-xbn2, mlld-qptr]
links: []
created: 2026-01-16T07:50:40.996831-08:00
type: task
priority: 0
parent: mlld-wmzl
---
# Standard policy modules (@mlld/*) - v4 structure

## Goal

Create standard policy modules that ship with mlld per spec v4 Part 10. Users can import these for sensible defaults.

## v4 Policy Structure

All standard policies use the v4 `defaults` object:

```mlld
policy {
  defaults: {
    unlabeled: untrusted,           // or trusted
    rules: [...],                   // Built-in rules to enable
    autosign: ["templates"],        // Auto-sign categories
    autoverify: true,               // Auto-verify for llm exes
    trustconflict: "warn"           // Conflict behavior
  },
  capabilities: { ... },
  sources: { ... },
  operations: { ... },
  // ...
}
```

## Standard Policies (v4 Syntax)

### @mlld/production

```mlld
policy {
  defaults: {
    unlabeled: untrusted,
    rules: [
      "no-secret-exfil",
      "no-sensitive-exfil",
      "no-untrusted-destructive",
      "no-untrusted-privileged",
      "untrusted-llms-get-influenced"
    ],
    autosign: ["templates"],
    autoverify: true
  },
  capabilities: {
    allow: {
      cmd: ["cat", "grep", "head", "tail", "ls", "jq", "git:*"],
      filesystem: {
        read: ["**"],
        write: ["/tmp/**"]
      }
    },
    deny: [sh, cmd: ["rm", "dd", "mkfs", "sudo", "chmod", "chown"]]
  },
  sources: {
    "src:mcp": untrusted,
    "src:network": untrusted
  },
  limits: {
    timeout: 30000
  }
}
```

### @mlld/development

```mlld
policy {
  defaults: {
    unlabeled: trusted,
    rules: ["no-secret-exfil"]  // Minimal - just protect secrets
  },
  capabilities: {
    deny: { cmd: [dd, mkfs, fdisk] }
  },
  limits: {
    timeout: 120000
  }
}
```

### @mlld/sandbox

```mlld
policy {
  defaults: {
    unlabeled: untrusted,
    rules: [
      "no-secret-exfil",
      "no-sensitive-exfil",
      "no-untrusted-destructive",
      "no-untrusted-privileged",
      "untrusted-llms-get-influenced"
    ]
  },
  capabilities: {
    allow: {
      cmd: [cat, grep, ls, head, tail],
      filesystem: {
        read: ["/workspace/**"],
        write: []
      }
    },
    deny: { sh, network, cmd: * }
  },
  limits: {
    timeout: 10000
  }
}
```

## Blocking Dependencies

This is blocked until:

1. **mlld-wmzl.15** - `defaults` object implementation
2. **mlld-dpgy** - Capability enforcement (allow-list)
3. **Signing beads** - `autosign`/`autoverify` implementation

## Exit Criteria

- [ ] @mlld/production policy file created
- [ ] @mlld/development policy file created
- [ ] @mlld/sandbox policy file created
- [ ] All use v4 `defaults` object structure
- [ ] Policies importable via `import policy from "@mlld/..."`
- [ ] Tests verify each policy blocks/allows expected operations

## References

- Spec v4 Part 10: Standard Policies
- Spec v4 Part 3.2: The `defaults` Object

## Estimated Effort

~3 hours (after dependencies complete)



