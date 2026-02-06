---
id: m-7e4d
status: closed
deps: []
created: 2026-02-05T14:06:04Z
type: task
priority: 2
assignee: Adam
tags: [phase-2, implementation]
updated: 2026-02-05T14:10:37Z
---
# Create reusable environment module with @spawn/@shell

## Context

Job: Package and Share Agent Configuration

All atoms exist (env-overview, env-config, policy-auth, policy-composition, modules-exporting). Target syntax validates. Now implement the working module.

## Requirements

1. Create module at `llm/modules/@examples/claude-dev/index.mld` with:
   - `profiles { full: {...}, readonly: {...} }`
   - `policy @config = { auth: { claude: {...} } }`
   - `exe @spawn(prompt)` using `using auth:claude`
   - `exe @shell()` using `using auth:claude`
   - `export { @spawn, @shell }`

2. Create usage example at `llm/modules/@examples/claude-dev-demo.mld` that:
   - Imports from the module
   - Calls `@spawn("test prompt")`
   - Demonstrates the import/use pattern

3. Validate both files pass `mlld validate`

## Target syntax from job

```mlld
>> @alice/claude-dev/index.mld
profiles {
  full: { requires: { sh, network } },
  readonly: { requires: { } }
}

policy @config = {
  auth: {
    claude: { from: "keychain:mlld-env-myproject/claude", as: "CLAUDE_CODE_OAUTH_TOKEN" }
  }
}

exe @spawn(prompt) = run cmd { claude -p "@prompt" } using auth:claude
exe @shell() = run cmd { claude } using auth:claude

export { @spawn, @shell }
```

Usage:
```mlld
import { @spawn } from "@alice/claude-dev"
var @result = @spawn("Fix the bug in auth.ts")
```

## Success criteria

- Module file exists and validates
- Demo file exists and validates
- Module demonstrates profiles, policy.auth, and credential injection patterns

## Use the mlld skill

Invoke `/mlld` to understand syntax. Run `mlld validate <file>` to verify syntax.


**2026-02-05 14:10 UTC:** Worker delivered working module with all required features: profile variants, keychain credential flow via policy.auth, and import/usage demonstration. Ready for Phase 3 verification.
