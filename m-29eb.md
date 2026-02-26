---
id: m-29eb
status: open
deps: []
created: 2026-02-25T19:15:32Z
type: chore
priority: 2
assignee: Adam Avenir
---
# Add lint and typecheck to CI

CI (tests.yml) only runs npm test. Lint and typecheck scripts exist in package.json but aren't gating PRs. Need to: get codebase passing full type check, get codebase passing lint, add both to CI workflow. This is a significant effort since the codebase doesn't currently pass a full type check.

