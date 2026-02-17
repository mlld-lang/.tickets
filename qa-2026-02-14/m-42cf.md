---
id: m-42cf
status: closed
deps: []
created: 2026-02-15T08:11:20Z
type: feature
priority: 2
tags: [qa-2026-02-14, run]
updated: 2026-02-15T09:55:44Z
---
# run-cwd: support @var, ~, and absolute paths

Currently cwd only supports absolute path literals. Fix to also support:
- @var references (mlld variables containing paths)
- ~ (tilde expansion to home directory)
- Absolute paths (already works)

No $VAR support (not valid mlld syntax in this context).

QA topic: run-cwd

## Acceptance Criteria

run cmd:~/dev/project {pwd} works, run cmd:@myPath {pwd} works

**2026-02-15 09:55 UTC:** Implemented run working-directory '~' expansion in resolver, preserved absolute-path behavior, and verified @var cwd references with regression tests in interpreter/eval/run.structured.test.ts. Updated run-cwd docs and validated with full npm test (4157 passed, 60 skipped).
