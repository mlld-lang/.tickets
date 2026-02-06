---
id: m-5e6d
status: closed
deps: []
created: 2026-02-03T05:58:37Z
type: bug
priority: 2
assignee: Adam
tags: [security, env-directive, phase-3]
updated: 2026-02-03T06:18:57Z
---
# Network restrictions not enforced in env blocks

The env directive allows setting net: 'none' to disable network access, but this is NOT enforced at runtime.

Evidence:
- env.ts stores the net config via setScopedEnvironmentConfig()
- No code anywhere checks the net setting before allowing network operations
- Commands can freely make network requests even with net: 'none'

Test case: env @config [run cmd { curl -s https://httpbin.org/get }] with net: 'none' successfully fetches the URL instead of blocking.

Impact: Security configuration is syntax-only; env network restrictions provide no actual security.


**2026-02-03 06:18 UTC:** Human confirmed via Claude investigation that Docker provider already enforces net restrictions. Closing as 'works as designed for provider case'.
