---
id: mlld-7si8
status: closed
deps: [mlld-n7c6]
links: []
created: 2026-01-14T15:21:40.836191-08:00
type: task
priority: 1
parent: mlld-als4
---
# Phase 6.0: mlld env command router

## Summary
Create the parent `mlld env` command that routes to subcommands.

## Context
After renaming `mlld env` → `mlld vars` (Phase 1.1), the `env` namespace is free for environment module commands.

## Implementation

### Command Structure

```bash
mlld env <subcommand> [args...]

Subcommands:
  list      List available environments
  capture   Create environment from current Claude config
  spawn     Run command with environment
  shell     Start interactive session
  export    Export environment template (v1.1)
  import    Import environment template (v1.1)
```

### Implementation

Location: `cli/commands/env.ts` (new file after vars rename)

```typescript
import { listEnvCommand } from './env/list';
import { captureEnvCommand } from './env/capture';
import { spawnEnvCommand } from './env/spawn';
import { shellEnvCommand } from './env/shell';

export async function envCommand(args: string[]): Promise<void> {
  const subcommand = args[0];
  const subArgs = args.slice(1);
  
  switch (subcommand) {
    case 'list':
    case 'ls':
      return listEnvCommand(subArgs);
      
    case 'capture':
      return captureEnvCommand(subArgs);
      
    case 'spawn':
      return spawnEnvCommand(subArgs);
      
    case 'shell':
      return shellEnvCommand(subArgs);
      
    case 'export':
    case 'import':
      console.error(`'mlld env ${subcommand}' coming in v1.1`);
      process.exit(1);
      
    default:
      printEnvHelp();
      process.exit(subcommand ? 1 : 0);
  }
}

function printEnvHelp(): void {
  console.log(`
mlld env - Manage AI agent environments

Usage: mlld env <command> [options]

Commands:
  list              List available environments
  capture <name>    Create environment from ~/.claude config
  spawn <name> -- <command>   Run command with environment
  shell <name>      Start interactive session

Examples:
  mlld env capture claude-dev
  mlld env list
  mlld env spawn claude-dev -- claude -p "Fix the bug"
  mlld env shell claude-dev
`.trim());
}
```

### Register in CommandDispatcher

Location: `cli/execution/CommandDispatcher.ts`

```typescript
import { envCommand } from './commands/env';

// In command map
'env': envCommand,
```

## Exit Criteria
- [ ] `mlld env` shows help
- [ ] `mlld env list` routes to list command
- [ ] `mlld env capture` routes to capture command
- [ ] `mlld env spawn` routes to spawn command
- [ ] `mlld env shell` routes to shell command
- [ ] Unknown subcommands show help with error exit code
- [ ] Help text is clear and complete

## Tests to Add
- Test subcommand routing
- Test help output
- Test unknown subcommand handling

## Dependencies
- Phase 1.1 (rename env → vars) - must complete first

## Estimated Effort
1 hour


