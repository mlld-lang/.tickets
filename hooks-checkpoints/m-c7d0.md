---
id: m-c7d0
status: closed
deps: []
created: 2026-02-19T23:02:01Z
type: bug
priority: 1
assignee: Adam Avenir
tags: [severity-high, pre-ga, polish]
updated: 2026-02-24T21:02:53Z
---
# Fix CLI docs: mlld init incorrectly documents module creation flags

## Problem

`docs/user/cli.md:107-131` documents `mlld init` under "Module Commands" as "Create a new mlld module interactively" with module creation flags (`--name`, `--author`, `--about`, `--version`, `--keywords`, `--homepage`, `--skip-git`, `--force`). This is wrong — `mlld init` is a project initialization command, not a module creation command.

**Code reality:**
- `cli/commands/init.ts:12-16` — `mlld init` creates `mlld-config.json`. Options: `--force`, `--script-dir`, `--local-path`
- `cli/commands/init-module.ts:23-35` — `mlld module` (alias `mlld mod`) creates modules. Options: `--name`, `--about`, `--author`, `--output`, `--skip-git`, `--version`, `--keywords`, `--homepage`, `--force`, `--type` (library|app|command|skill|environment), `--global`
- `cli/execution/CommandDispatcher.ts:56-58` — registers `init`, `module`, and `mod` as separate commands

## Required changes in `docs/user/cli.md`

### 1. Rewrite the `mlld init` section (lines 107-131)

Move it out of "Module Commands" into the "Configuration Commands" section (near `mlld setup` at line 524). Rewrite to describe project initialization:

```markdown
### `mlld init`

Initialize a new mlld project. Creates `mlld-config.json` with default settings.

\`\`\`bash
# Initialize project in current directory
mlld init

# Override default script directory
mlld init --script-dir scripts/

# Override default local module path
mlld init --local-path ./modules

# Force overwrite existing config
mlld init --force
\`\`\`

**Options:**
- `--script-dir <path>` - Script directory (default: `llm/run`)
- `--local-path <path>` - Local module base path (default: `./llm/modules`)
- `-f, --force` - Overwrite existing `mlld-config.json`
```

### 2. Fix the tutorial reference at line 923

Change `mlld init my-utils.mld.md` to `mlld module my-utils.mld.md`.

### 3. Check for other stale `mlld init` references

Search for any other place in the docs that shows `mlld init` with module creation flags or `mlld init <filename>` and update to `mlld module`.

## Acceptance criteria

- `mlld init` section describes project initialization with `--force`, `--script-dir`, `--local-path` only.
- `mlld init` section is under "Configuration Commands", not "Module Commands".
- No references to `mlld init <filename>` remain in the docs (those should be `mlld module <filename>`).
- See also m-f4d2 which adds the `mlld module` section — these two tickets should be done together.

