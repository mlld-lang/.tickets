---
id: mlld-ppath
status: closed
deps: []
links: []
created: 2026-01-20T08:46:46.925895-08:00
type: feature
priority: 1
---
# Path pattern syntax in capabilities (path:)

## Summary

Add `fs:` prefix pattern syntax for filesystem capabilities, working uniformly across allow, deny, and danger.

## Syntax

Flat patterns consistent with `cmd:` prefix:

```mlld
capabilities: {
  allow: [
    "cmd:git:*",
    "cmd:npm:*",
    "fs:r:**",                // read anything
    "fs:w:@base/tmp/**",      // read+write to tmp (write implies read)
    "fs:w:@base/dist/**"      // read+write to dist
  ],
  
  deny: [
    "fs:w:/etc/*"             // no writing to /etc
  ]
}

// In danger list
danger: [
  "fs:r:~/.ssh/*",
  "fs:r:~/.aws/*",
  "fs:w:.claude/*"
]
```

## Permission Model

Two permissions:
- `fs:r:{path}` - read only
- `fs:w:{path}` - read + write (write implies read)
- `fs:rw:{path}` - alias for `fs:w:` (ergonomic shorthand)

## Shorthand Expansion

- `fs` → `fs:w:**` (full filesystem access)
- `fs:r` → `fs:r:**` (read everything)
- `fs:w` → `fs:w:**` (write everything)
- `fs:rw` → `fs:w:**` (alias for fs:w)

## Path Patterns

- `**` - Match any path/depth
- `*` - Match single path segment
- `@base` - Project root (location of mlld-config.json)
- `~` - Home directory
- Relative paths relative to `@base`

## Examples

| Pattern | Matches |
|---------|---------|
| `fs:r:**` | Read anything |
| `fs:w:@base/tmp/**` | Read+write under project tmp/ |
| `fs:rw:@base/tmp/**` | Same as above (rw = w) |
| `fs:r:~/.ssh/*` | Read direct children of ~/.ssh |
| `fs:w:.claude/*` | Read+write direct children of .claude/ |
| `fs:r:**/.env` | Read any .env file anywhere |

## Integration Points

1. `allow` list - Permitted filesystem access
2. `deny` list - Blocked paths
3. `danger` list - Dangerous paths requiring explicit opt-in
4. File read operations (`<file>`, `/import`)
5. File write operations (`/output`, `/append`)

## Replaces

This replaces the boolean `filesystem` capability. For backwards compatibility:
- `allow: [filesystem]` → `allow: ["fs:w:**"]`

## Implementation

1. Add fs pattern parser to `core/policy/`
2. Add `matchesFsPattern(path, pattern, mode)` utility
3. Integrate into capability enforcement
4. Handle `@base` and `~` expansion
5. Normalize `rw` → `w` during parsing
6. Update existing `filesystem` boolean to expand to `fs:w:**`

## Exit Criteria

- [ ] `fs:r:` and `fs:w:` patterns parse in allow, deny, danger
- [ ] `fs:rw:` accepted as alias for `fs:w:`
- [ ] Write implies read (no separate rw semantics)
- [ ] `@base` expands to project root
- [ ] `~` expands to home directory
- [ ] Glob patterns work (`*`, `**`)
- [ ] File read operations check `fs:r:` patterns
- [ ] File write operations check `fs:w:` patterns
- [ ] Danger list fs patterns enforced
- [ ] Shorthand expansion works (`fs`, `fs:r`, `fs:w`, `fs:rw`)
- [ ] Backwards compat: `filesystem` → `fs:w:**`
- [ ] Tests for pattern matching



