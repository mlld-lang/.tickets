---
id: mlld-dpgy
status: closed
deps: [mlld-905x, mlld-wmzl.15, mlld-ppath]
links: []
created: 2026-01-15T11:04:01.349021-08:00
type: bug
priority: 1
parent: mlld-urik
---
# Policy capability enforcement (v4 syntax)

## Problem

Policy capability enforcement is incomplete. Only `/run` denies work. Missing:
- Allow-list enforcement (whitelist)
- Full capability pattern syntax from v4 spec

## v4 Capability Syntax

See `todo/spec-security-2026-v4.md` Part 3.3 for authoritative syntax:

```mlld
capabilities: {
  allow: {
    js,                              // Shorthand for js: ["*"]
    cmd: [
      "git:*",                       // git with any subcommands
      "npm install:*",               // npm install with any args
      "npm run test:*",              // npm run test with any args
      "mlld:*"                       // mlld CLI commands
    ],
    network: {
      domains: ["*.github.com", "api.anthropic.com"]
    },
    filesystem: {
      read: ["**"],
      write: ["@base/tmp/**", "@base/dist/**"]
    }
  },
  deny: [sh, py]                     // Shorthand: sh: ["*"], py: ["*"]
}
```

**Pattern syntax** (matches Claude Code style):
- `cmd` = category, `cmd: ["*"]` = all commands
- `git:*` = git with any subcommands/args
- `git status` = exact match, no additional args
- `git status:*` = git status with any args

**Shorthand expansion:**
```mlld
allow: [cmd, js]        → allow: { cmd: ["*"], js: ["*"] }
deny: [sh]              → deny: { sh: ["*"] }
```

## Current State

From `core/policy/guards.ts`:
- Generates guards for: `op:cmd` with deny rules only
- Does NOT enforce: allow-list constraints (whitelist)
- Does NOT support: full pattern syntax

## Required Changes

### 1. Pattern Matching

Implement pattern matching for capability rules:

```typescript
function matchesCapabilityPattern(op: string, pattern: string): boolean {
  // "git:*" matches "git", "git:status", "git:push:--force"
  // "git status" matches only "git status" exactly
  // "git status:*" matches "git status" and "git status:args"
}
```

### 2. Allow-List Enforcement

```mlld
policy { allow: { cmd: [git, npm] } }
// Should block: curl, wget (not in whitelist)
// Currently: NOT BLOCKED
```

Generate guards that deny unlisted commands when allow-list is present.

### 3. Filesystem/Network Patterns

```mlld
filesystem: {
  read: ["**"],
  write: ["@base/tmp/**"]
}
```

Path patterns relative to `@base` (mlld-config.json location).

## Exit Criteria

- [ ] Pattern matching implemented (git:*, git status, etc.)
- [ ] Allow-list enforcement (whitelist) works
- [ ] Deny-list enforcement works
- [ ] Filesystem path patterns work
- [ ] Network domain patterns work
- [ ] All generated guards are privileged

## References

- Spec v4 Part 2.2: Policy-Based Capability Control
- Spec v4 Part 3.3: Capability Syntax

## Estimated Effort

6-8 hours



