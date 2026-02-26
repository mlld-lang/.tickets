---
id: m-b51d
status: closed
deps: [m-219f]
created: 2026-02-25T15:36:59Z
type: bug
priority: 0
updated: 2026-02-25T19:31:45Z
---
# show directive does not use streaming adapter

## Bug

`show @streamingExe("prompt")` does not route output through the format adapter. It displays raw command output (e.g., raw NDJSON) instead of adapter-parsed text.

This is likely a consequence of m-219f (stream in exe definition not activating the adapter pipeline). If fixing m-219f also fixes `show`, close this ticket. If not, `show` has its own issue with streaming propagation.

## Reproduce

Create `tmp/repro-show-stream.mld`:

```mlld
exe @llm(p) = stream cmd {echo '{"type":"text","text":"hello"}'} with { streamFormat: "claude-code" }
show @llm("test")
```

Run: `mlld tmp/repro-show-stream.mld`

**Actual:** Raw JSON printed.
**Expected:** `hello` printed.

## What to fix

First fix m-219f, then re-test this repro. If it still dumps raw JSON, then `show` has its own code path that bypasses streaming. Investigate how `show` invokes the exe and whether it passes through the streaming context.

## Verify

The repro prints `hello`, not raw JSON.

## Acceptance Criteria

`show @streamingExe()` displays parsed adapter output when the exe has streaming + streamFormat configured.

**2026-02-25 19:31 UTC:** Show invocation now correctly routes streaming definition output without duplicate emissions (commit 938cc7967).
