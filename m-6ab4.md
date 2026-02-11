---
id: m-6ab4
status: open
deps: []
links: []
created: 2026-02-06T20:37:31Z
type: task
priority: 0
assignee: Adam Avenir
---
# mlld init generates wrong config shape for resolver prefixes

`mlld init` generates `resolverPrefixes` (top-level array) in `mlld-config.json`, but the runtime reads `resolvers.prefixes` (nested object). This means any project scaffolded with `mlld init` that sets up a @local/ prefix will silently fail at runtime.

Related: m-f554 (the silent ignore + confusing error when prefix config is missing)

The init command should generate:
```json
{
  "resolvers": {
    "prefixes": [...]
  }
}
```

Not:
```json
{
  "resolverPrefixes": [...]
}
```

Discovered when `mlld init` scaffolded inj-defense project and `@local/claude-poll` failed with 'not found in local\'s registry'.

