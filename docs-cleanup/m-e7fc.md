---
id: m-e7fc
status: closed
deps: []
created: 2026-02-17T23:13:04Z
type: task
priority: 2
assignee: Adam Avenir
updated: 2026-02-18T07:52:02Z
---
# Fold STREAMING-ADAPTERS.md into STREAMING.md

Move streaming adapter architecture into docs/dev/STREAMING.md.

## Acceptance Criteria

- docs/dev/STREAMING.md documents adapter architecture and usage boundaries.\n- docs/dev/STREAMING-ADAPTERS.md is ready for deletion by the remove ticket.


**2026-02-17 23:37 UTC:** Doc-audit input (source: `llm/run/doc-audit/tmp/results.json`) for this ticket.
This input MUST be verified with 100% confidence during implementation.

Files in scope to verify:
- `docs/dev/STREAMING.md` [outdated]: Several interface definitions and file paths are wrong; StructuredResult.streaming shape is substantially incorrect; guard check file is misattributed

Verification bar (required):
- Confirm or disprove each finding for the files above against current code/docs with 100% confidence.
- If disproved, record why with concrete file references.
- If confirmed, update docs in this ticket scope and keep links/frontmatter consistent.

**2026-02-18 00:20 UTC:** Verified against current code. Decisive merge list for folding `docs/dev/STREAMING-ADAPTERS.md` into `docs/dev/STREAMING.md`:

## `docs/dev/STREAMING.md`

- Keep `STREAMING.md` as canonical and fold in adapter architecture content from `STREAMING-ADAPTERS.md` (adapter contracts, registry, JSONPath extraction, template interpolation, sink accumulation behavior), but align all types/paths to current implementation.
- Fix adapter/type definitions to match current sources:
  - `StreamAdapter.format` is `'ndjson' | 'sse' | 'text'` (not `'custom'`).
  - `StreamAdapter` includes `reset()`.
  - `ParsedEvent.raw` and `ParsedEvent.timestamp` are optional.
  - `ParsedEvent.templates` uses `EventTemplate` (`text`/`ansi`/`json`), not `Record<string,string>`.
  - Source of truth: `interpreter/streaming/adapters/base.ts`.
- Correct SDK streaming event names to hyphenated forms everywhere:
  - `streaming:tool-use`, `streaming:tool-result` (not underscore variants).
  - Source of truth: `sdk/types.ts`, `interpreter/eval/pipeline/stream-sinks/format-adapter.ts`.
- Correct `StructuredResult.streaming` shape to current `StreamingResult` contract:
  - `text`, `thinking`, `toolCalls`, `usage`, `errors`, `events` (all optional).
  - Remove nested `{ accumulated: ..., events: ParsedEvent[] }` structure.
  - Clarify `events` are SDK streaming events and are only present when `keepRawEvents` is true.
  - Source of truth: `sdk/types.ts`, `interpreter/index.ts`, `interpreter/streaming/streaming-manager.ts`, `interpreter/eval/pipeline/stream-sinks/format-adapter.ts`.
- Fix guard attribution:
  - Streaming after-guard denial check is implemented in `interpreter/hooks/guard-post-orchestrator.ts`.
  - `/run` has an additional pre-check in `interpreter/eval/run.ts`.
  - `interpreter/hooks/guard-post-hook.ts` is a delegating wrapper, not where the streaming check logic lives.
- Fix sink-path behavior description:
  - Do not claim all streaming uses the adapter path.
  - `StreamingManager` uses `FormatAdapterSink` only when an adapter is configured.
  - Without an adapter, default path is `TerminalSink` + `ProgressOnlySink`.
- Expand built-in adapter list/aliases to current registry contents:
  - include `claude-code`, `claude-agent-sdk`, `@mlld/claude-agent-sdk`, `anthropic`, `ndjson`.
  - Source: `interpreter/streaming/adapter-registry.ts`.
- When carrying test/path references, use current concrete paths (e.g. `tests/helpers/stream-recorder.ts`, `interpreter/env/executors/streaming.integration.test.ts`) rather than ambiguous bare filenames.

## `docs/dev/STREAMING-ADAPTERS.md`

- After migration, reduce to a short pointer stub and mark ready for deletion by the remove ticket.
- Do not keep duplicate standalone adapter architecture once STREAMING is updated.
