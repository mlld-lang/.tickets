---
id: mlld-dgcu
status: open
deps: []
links: []
created: 2026-01-15T10:55:50.364453-08:00
type: task
priority: 2
parent: mlld-urik
---
# D3-GAP: Document mlld env commands

## Problem

`mlld env` commands are fully implemented but not documented in `docs/user/cli.md`.

## Current State

cli.md documents 14+ commands but NOT mlld env:
- init, publish, install, ls, info, clean, add-needs, run, test, mlldx, setup, alias, auth, vars, registry

The implementation exists in `cli/commands/env.ts` with full help text (lines 628-649).

## Required Documentation

Add to `docs/user/cli.md`:

```markdown
### mlld env

Manage AI agent environment modules. Environments package credentials,
configuration, and security policies for spawning AI agents.

#### Subcommands

**mlld env list** / **mlld env ls**
List available environment modules.

\`\`\`bash
mlld env list
mlld env list --json
\`\`\`

Shows environments in `.mlld/env/` (local) and `~/.mlld/env/` (global).

---

**mlld env capture <name>**
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

---

**mlld env spawn <name> -- <command>**
Run a command with an environment's credentials.

\`\`\`bash
mlld env spawn my-claude -- claude -p "Fix the bug"
\`\`\`

Loads the environment module, resolves /wants against policy,
and executes with credentials injected.

---

**mlld env shell <name>**
Start an interactive agent session.

\`\`\`bash
mlld env shell my-claude
\`\`\`

---

**mlld env export / import**
Coming in v1.1.
```

## Also Document

- Environment module structure (module.yml with type: environment)
- Required exports (@spawn, @shell)
- Security: tokens stored in keychain, not files

## Files to Modify

1. `docs/user/cli.md` - Add mlld env section

## Exit Criteria

- [ ] All mlld env subcommands documented
- [ ] Examples provided for each command
- [ ] Environment module structure explained
- [ ] Security notes included

## Estimated Effort
2-3 hours


