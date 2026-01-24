---
id: mlld-il3y
status: open
deps: []
links: []
created: 2026-01-14T20:06:29.023293-08:00
type: task
priority: 2
parent: mlld-su55
---
# D3: Document mlld env commands

## Summary

Add documentation for the new `mlld env` commands to user docs.

## Current State

`docs/user/cli.md` documents `mlld vars` but not `mlld env`.

## Documentation to Add

### Section: mlld env

```markdown
### mlld env

Manage AI agent environment modules. Environments package credentials,
configuration, and security policies for spawning AI agents.

#### mlld env list

List available environment modules.

\`\`\`bash
mlld env list
mlld env list --json
\`\`\`

Shows environments in:
- `.mlld/env/` (local)
- `~/.mlld/env/` (global)

#### mlld env capture <name>

Create an environment module from your current Claude configuration.

\`\`\`bash
mlld env capture my-claude
mlld env capture my-claude --global
\`\`\`

This:
1. Extracts OAuth token from `~/.claude/.credentials.json`
2. Stores token securely in macOS Keychain
3. Copies settings.json, CLAUDE.md, hooks.json
4. Generates module.yml and index.mld

#### mlld env spawn <name> -- <command>

Run a command with an environment's credentials and config.

\`\`\`bash
mlld env spawn my-claude -- claude -p "Fix the bug"
\`\`\`

This loads the environment module, resolves /wants against policy,
and invokes the exported @spawn function.

#### mlld env shell <name>

Start an interactive agent session.

\`\`\`bash
mlld env shell my-claude
\`\`\`

Equivalent to calling the environment's exported @shell function.
```

### Environment Module Structure

Document the expected structure:

```markdown
## Environment Modules

An environment module packages everything for spawning an AI agent:

\`\`\`
.mlld/env/my-claude/
├── module.yml       # type: environment
├── index.mld        # Main module with @spawn, @shell exports
└── .claude/         # Agent-specific config
    ├── settings.json
    ├── CLAUDE.md
    └── hooks.json
\`\`\`

### Required Exports

- `@spawn(prompt)` - Run agent with prompt
- `@shell()` - Interactive session

### Optional Exports  

- `@mcpConfig()` - MCP server configuration
```

## Files to Modify

1. `docs/user/cli.md` - Add mlld env section
2. Consider separate `docs/user/environments.md` for detailed semantics

## Exit Criteria

- [ ] mlld env commands documented in cli.md
- [ ] Environment module structure explained
- [ ] Examples show common usage
- [ ] Security considerations noted

## Estimated Effort
2-3 hours


