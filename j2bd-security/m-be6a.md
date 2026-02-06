---
id: m-be6a
status: closed
deps: []
created: 2026-02-03T05:37:31Z
type: task
priority: 2
assignee: Adam
tags: [phase-1, doc]
updated: 2026-02-03T05:41:22Z
---
# Atom: policy-auth - credential injection via using auth

Create policy-auth atom explaining credential injection via `using auth:*` syntax.

Must cover:
- policy.auth configuration (from/as fields)
- `using auth:*` syntax for credential injection
- Sealed credential paths (secrets flow from keychain to env var, never become strings)
- Why this bypasses label deny rules (env var injection, not interpolation)

Refer to spec Part 3.4 for details on credential flow via using keyword.

Target: docs/src/atoms/security/policy-auth.md
100-200 words with working mlld examples that pass `mlld validate`.

