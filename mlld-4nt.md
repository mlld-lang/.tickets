---
id: mlld-4nt
status: closed
deps: []
links: []
created: 2025-12-11T20:51:09.910316-08:00
type: bug
priority: 2
---
# CI: gray-matter tests failing in GitHub Actions

Three tests fail in CI but pass locally:
- slash/exe/exe-node-gray-matter-access
- slash/exe/exe-node-mlld-dependencies  
- slash/exe/exe-node-shadow-env-always-created

Error: `Command execution failed: node: const matter = require('gray-matter');`

## Root Cause
Likely missing `gray-matter` dependency in CI environment or NODE_PATH issue.

## Local Behavior
All tests pass locally (2527/2527)

## Impact
CI builds fail even though code is correct

## Investigation Needed
- Check if gray-matter is in package.json dependencies
- Verify NODE_PATH includes node_modules in CI
- May need to add gray-matter as explicit dependency vs peer dependency


