---
id: m-c9e8
status: closed
deps: []
created: 2026-02-25T15:36:58Z
type: bug
priority: 0
updated: 2026-02-25T19:31:45Z
---
# --show-json flag does not output anything

## Bug

The `--show-json` CLI flag is supposed to mirror raw NDJSON events to stderr during streaming. The flag is parsed and a variable is created, but nothing subscribes to streaming events to write them to stderr.

## Reproduce

Create `tmp/repro-show-json.mld`:

```mlld
exe @llm(p) = cmd {echo '{"type":"text","text":"hello"}'}
run stream @llm("test") with { streamFormat: "claude-code" }
```

Run: `mlld tmp/repro-show-json.mld --show-json 2>tmp/stderr.txt`

Check: `cat tmp/stderr.txt`

**Actual:** `tmp/stderr.txt` is empty (or has no JSON).
**Expected:** `tmp/stderr.txt` contains the raw NDJSON events.

## What to fix

Wire the `--show-json` flag through from CLI parsing to the streaming pipeline. When enabled, each raw NDJSON chunk received by the streaming system should also be written to stderr. This could be a new sink, or integrated into an existing sink. The OptionProcessor needs to pass this flag into StreamingOptions, and the StreamingManager (or a sink) needs to act on it.

## Verify

Run the repro with `--show-json` and confirm raw JSON lines appear on stderr. Formatted output should still appear on stdout.

## Acceptance Criteria

`mlld script.mld --show-json` mirrors every raw NDJSON line to stderr during streaming execution.

**2026-02-25 19:31 UTC:** Wired --show-json through streaming options and raw NDJSON mirror sink with tests (commit 938cc7967).
