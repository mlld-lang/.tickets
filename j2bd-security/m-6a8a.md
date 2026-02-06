---
id: m-6a8a
status: closed
deps: []
created: 2026-02-03T21:25:53Z
type: task
priority: 2
assignee: Adam
tags: [urgency-high, phase-3, remediation]
updated: 2026-02-03T21:32:41Z
---
# Expand deny:['sh'] to block all shells and wrapper bypasses

## Problem

Adversarial testing (m-ed11) found two bypasses of `deny: ['sh']`:

1. **Alternative shells**: `run cmd { zsh -c "..." }` bypasses because only 'sh' and 'bash' are checked
2. **Command wrappers**: `run cmd { env sh -c "..." }` bypasses because only the first token is checked

## Fix Location

`core/policy/guards.ts`, function `evaluateCommandAccess`, lines 341-350.

## Current Code

```typescript
if (denyMap && isDenied('sh', denyMap)) {
  const firstWord = commandTokens[0]?.toLowerCase() ?? '';
  if (firstWord === 'sh' || firstWord === 'bash') {
    return {
      allowed: false,
      commandName,
      reason: 'Shell access denied by policy'
    };
  }
}
```

## Required Changes

1. **Expand shell list**: Replace the `sh`/`bash` check with a Set of known shell binaries:
   ```
   const SHELL_BINARIES = new Set(['sh', 'bash', 'zsh', 'dash', 'fish', 'csh', 'tcsh', 'ksh', 'ash']);
   ```

2. **Handle wrapper commands**: Known wrappers like `env`, `nice`, `nohup`, `timeout`, `strace` can hide shell invocations. Scan the first few tokens to find the actual command:
   ```
   const COMMAND_WRAPPERS = new Set(['env', 'nice', 'nohup', 'timeout', 'strace', 'time']);
   ```
   Skip wrapper tokens (and their flag arguments like `-u` for env) to find the real command, then check that against SHELL_BINARIES.

3. **Also strip path prefixes**: Check for `/bin/sh`, `/usr/bin/env sh`, etc. - extract just the basename from tokens before matching.

## Testing

- `run cmd { zsh -c "echo test" }` with `deny: ['sh']` → MUST be blocked
- `run cmd { env sh -c "echo test" }` with `deny: ['sh']` → MUST be blocked  
- `run cmd { env bash -c "echo test" }` with `deny: ['sh']` → MUST be blocked
- `run cmd { nice sh -c "echo test" }` with `deny: ['sh']` → MUST be blocked
- `run cmd { git status }` with `deny: ['sh']` → MUST still be allowed
- `run cmd { grep sh file.txt }` → MUST still be allowed (sh is not first/wrapper-resolved token)
- All existing tests must pass: `npm test`

Use the adversarial test files already in tmp/ for verification:
- `tmp/adversarial-test2d-zsh-bypass.mld` (should now fail with denial)
- `tmp/adversarial-test2e-env-bypass.mld` (should now fail with denial)

