---
id: m-ab32
status: open
deps: []
links: []
created: 2026-01-25T12:24:57Z
type: feature
priority: 2
assignee: Adam Avenir
tags: [interpreter, cmd, reliability]
---
# Add native timeout support for cmd/run directives

When running long-running commands via cmd/run directives, there's no way to set a timeout. This can cause mlld scripts to hang indefinitely if a subprocess doesn't exit.

## Problem Encountered

While running qa.mld (parallel QA testing with claude sessions), one of 39 parallel claude processes completed its work but never exited. The mlld script hung waiting for that process indefinitely. The process had:
- Completed all its work (wrote output files)
- Used only 19 seconds of CPU in over an hour
- No active network connections
- Simply never terminated

## Current Workaround Options

1. **Use Unix timeout command** (not available on macOS by default):
   ```mlld
   let @response = sh { timeout 30m claude -p ... || true }
   ```

2. **Use gtimeout from GNU coreutils** (requires brew install coreutils):
   ```mlld
   let @response = sh { gtimeout 30m claude -p ... || true }
   ```

3. **Manual monitoring** - Kill stale processes externally

## Proposed Solution

Add native timeout support to cmd/run directives:

```mlld
>> Option A: Modifier syntax
let @result = cmd:timeout(30m) { long-running-command }

>> Option B: Pipeline modifier  
let @result = cmd { long-running-command } | timeout(30m)

>> Option C: Environment-level setting
env { timeout: "30m" } [
  run cmd { long-running-command }
]
```

## Considerations

- Should support duration formats (30s, 5m, 1h)
- Need to decide behavior on timeout: error vs empty result vs special status
- Consider SIGTERM first, then SIGKILL after grace period
- Should work with streaming commands too

