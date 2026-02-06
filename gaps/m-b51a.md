---
id: m-b51a
status: closed
deps: [m-8fad, m-80dc]
created: 2026-02-05T03:10:07Z
type: task
priority: 2
assignee: Adam
updated: 2026-02-05T09:09:00Z
---
# Sign/verify infrastructure exists but end-to-end demo unproven

**Problem**: Sign/verify infrastructure is implemented and tested, but the actual use case (preventing prompt injection in LLM-to-LLM flows) has no working demo.

**What works**:
- SignatureStore, auto-sign, auto-verify all implemented ✓
- Unit tests pass ✓

**What's missing/unproven**:
- No demo with real LLM calls showing prompt injection defense
- Job spec wanted end-to-end flow with real LLMs
- Delivered: printf mock commands

**Evidence**: docs/src/atoms/security/pattern-audit-guard.md uses `printf` instead of real LLM

**Questions**:
1. Can LLM actually call `mlld verify` as a tool?
2. Does MLLD_VERIFY_VARS injection work with real claude CLI?
3. Does autoverify template prepending work?

**Expected**: Working demo showing signing prevents prompt injection in actual LLM-to-LLM flows.

**Files**: Demo needed, possibly integration gaps

## Plan
- Use Claude CLI via `@mlld/claude` (see `modules/llm/modules/claude.mld.md`) and codify the expected tool call flow (`mlld verify`, `MLLD_VERIFY_VARS` injection, signed template usage).
- Add an end-to-end demo script (likely under `llm/run/j2bd/security/impl/`) that runs the real Claude CLI when `ANTHROPIC_API_KEY` is present and falls back to a stub when not.
- Validate autoverify injection in the demo: ensure the LLM sees the verification instructions and calls `mlld verify` before acting.
- Add an env-gated integration test to exercise the real CLI flow without breaking CI.
- Update the security docs and the existing pattern-audit-guard example to reference the real demo and include run instructions.
