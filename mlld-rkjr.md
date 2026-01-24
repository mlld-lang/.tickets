---
id: mlld-rkjr
status: open
deps: []
links: []
created: 2026-01-15T11:04:41.806116-08:00
type: bug
priority: 1
parent: mlld-urik
---
# HIGH: @spawn signature mismatch between template and CLI

## Problem

Template expects `@spawn(prompt)` with a string, but CLI passes command argv array.

## Evidence

From gpt52:
- `cli/commands/env.ts:508` - Template defines: `/exe @spawn(prompt) = ...`
- `cli/commands/env.ts:537` - CLI calls: `executeEnvExport(loaded, 'spawn', command)`
  where `command` is `string[]` (argv array)

## Generated Template (env.ts:508)

```mlld
/exe @spawn(prompt) = \
  CLAUDE_CODE_OAUTH_TOKEN=@token \
  CLAUDE_CONFIG_DIR=@fm.dir/.claude \
  claude -p @prompt
```

Expects: `@spawn("Fix the bug")` - single prompt string

## CLI Invocation (env.ts:537)

```typescript
// command = ['claude', '-p', 'Fix the bug']
await executeEnvExport(loaded, 'spawn', command);
```

Passes: argv array, not prompt string

## Options

### Option A: Fix CLI to pass prompt string

```typescript
// Parse -- separator and extract prompt
const promptArgs = command.slice(1);  // Skip command name
const prompt = promptArgs.join(' ');
await executeEnvExport(loaded, 'spawn', [prompt]);
```

### Option B: Fix template to accept argv

```mlld
/exe @spawn(argv) = \
  CLAUDE_CODE_OAUTH_TOKEN=@token \
  @argv
```

### Option C: Support both patterns

Template detects whether it got a string or array and handles both.

## Recommendation

**Option A** - CLI should parse and pass prompt string. This matches:
- User expectation: `mlld env spawn my-env -- claude -p "Fix bug"`
- The `-- ` separator already indicates "rest is the command"
- Template pattern: agent environments receive prompts, not raw argv

## Exit Criteria

- [ ] `mlld env spawn my-env -- claude -p "Fix bug"` works
- [ ] @spawn receives prompt string, not argv array
- [ ] Template @spawn(prompt) signature makes sense
- [ ] Test verifies correct invocation

## Estimated Effort
2-3 hours


