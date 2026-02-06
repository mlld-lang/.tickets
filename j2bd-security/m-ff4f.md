---
id: m-ff4f
status: closed
deps: []
created: 2026-02-04T07:05:13Z
type: task
priority: 2
assignee: Adam
tags: [j2bd-pkg-env, phase-2]
updated: 2026-02-04T07:11:27Z
---
# Phase 2: Create reusable environment module with profiles and credential flow

Create the Phase 2 implementation for the package-env job.

## What to build

1. **Environment module** at `pipeline/env-module/index.mld` (or similar) that:
   - Declares `profiles { full: { requires: { sh, network } }, readonly: { requires: { } } }`
   - Configures `policy @config = { auth: { claude: { from: "keychain:mlld-env-myproject/claude", as: "CLAUDE_CODE_OAUTH_TOKEN" } } }`
   - Defines `exe @spawn(prompt) = run cmd { claude -p "@prompt" } using auth:claude`
   - Defines `exe @shell() = run cmd { claude } using auth:claude`
   - Exports `{ @spawn, @shell }`

2. **Consumer script** at `pipeline/env-module-demo.mld` that:
   - Imports from the module: `import { @spawn } from "./env-module"`
   - Calls @spawn with a prompt
   - Shows the result

## Reference

- Existing working example: `tests/cases/docs/cli/04/example.mld` (uses policy auth, using auth:, export)
- profiles evaluator: `interpreter/eval/profiles.ts`
- Target code from job spec: see j2bd/security/jobs/package-env.md lines 68-93

## Validation

- Both files must pass `mlld validate`
- The module should demonstrate the full sharing pattern from the job scenario
- The consumer script should show how another user would import and use the module


**2026-02-04 07:11 UTC:** Phase 2 deliverables: pipeline/env-module/index.mld, pipeline/env-module-demo.mld. Both validate. Commit cbfbd424d.
