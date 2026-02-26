---
id: m-f4d2
status: closed
deps: []
created: 2026-02-20T16:05:53Z
type: task
priority: 2
assignee: Adam Avenir
tags: [severity-medium, pre-ga]
updated: 2026-02-24T21:02:53Z
---
# Document mlld module / mlld mod command

## Problem

`mlld module` (alias: `mlld mod`) is registered in `cli/execution/CommandDispatcher.ts:57-58` and fully implemented in `cli/commands/init-module.ts`, but has no section in `docs/user/cli.md`. The module creation flags are currently misattributed to `mlld init` (see m-c7d0). These two tickets should be done together.

## Required change

Add a `mlld module` section under "Module Commands" in `docs/user/cli.md` (where the current wrong `mlld init` section is at line 107). This replaces the module creation content that m-c7d0 removes from the `mlld init` section.

```markdown
### `mlld module [output]` (alias: `mlld mod`)

Create a new mlld module interactively or with flags.

\`\`\`bash
# Interactive creation
mlld module

# Create specific module file
mlld module utils.mld.md

# Non-interactive with metadata
mlld module --name utils --author alice --about "Utility functions"

# Create a skill module
mlld module --type skill --name my-skill

# Create in a resolver path
mlld module -o @local/my-utils
\`\`\`

**Options:**
- `-n, --name <name>` - Module name
- `-a, --author <author>` - Author name
- `-d, --about <description>` - Module description
- `-o, --output <path>` - Output file path (supports `@resolver/name` syntax)
- `--version <version>` - Module version (default: 1.0.0)
- `-k, --keywords <keywords>` - Comma-separated keywords
- `--homepage <url>` - Homepage URL
- `--type <type>` - Module type: `library`, `app`, `command`, `skill`, `environment` (aliases: `lib`, `env`)
- `--global` - Create as a global module
- `--skip-git` - Skip git integration
- `-f, --force` - Overwrite existing files
```

Source for options: `cli/commands/init-module.ts:23-35` (`InitModuleOptions` interface) and line 20 for valid module types.

## Acceptance criteria

- `mlld module` section exists under "Module Commands" in `docs/user/cli.md`.
- All options from `InitModuleOptions` are documented including `--type` and `--global` (which the old misplaced docs omitted).
- Alias `mlld mod` is mentioned.
- Examples show interactive, non-interactive, and typed module creation.

