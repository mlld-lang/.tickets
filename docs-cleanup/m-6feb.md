---
id: m-6feb
status: closed
deps: []
created: 2026-02-17T23:13:04Z
type: task
priority: 2
assignee: Adam Avenir
updated: 2026-02-18T07:24:43Z
---
# Fold LARGE-VARIABLES.md into DATA.md

Integrate large-variable handling architecture into docs/dev/DATA.md.

## Acceptance Criteria

- Large-variable boundary behavior is documented in docs/dev/DATA.md.\n- docs/dev/LARGE-VARIABLES.md is ready for deletion by the remove ticket.


**2026-02-17 23:37 UTC:** Doc-audit input (source: `llm/run/doc-audit/tmp/results.json`) for this ticket.
This input MUST be verified with 100% confidence during implementation.

Files in scope to verify:
- `docs/dev/DATA.md` [mostly-current]: Largely accurate; two medium-severity issues: incorrect file path/line reference for show.ts:630 (file is only 158 lines; pattern lives in show/show-invocation-handlers.ts:56), and the Pipelines section misattributes structuredOutputs/previousOutputs to PipelineExecutor when they actually live in state-machine.ts context building.

Verification bar (required):
- Confirm or disprove each finding for the files above against current code/docs with 100% confidence.
- If disproved, record why with concrete file references.
- If confirmed, update docs in this ticket scope and keep links/frontmatter consistent.

**2026-02-17 23:56 UTC:** Verified against current code/docs (100% confidence). Decisive update list:

- `docs/dev/DATA.md`
  - Apply the two confirmed audit fixes:
    - Replace bad reference `interpreter/eval/show.ts:630` with the real nested-wrapper conversion location in `interpreter/eval/show/show-invocation-handlers.ts` (`toInvocationDisplayValue`, current line ~56).
    - Correct pipeline ownership wording: `previousOutputs` / `structuredOutputs` context assembly lives in `interpreter/eval/pipeline/state-machine.ts` (`buildStageContext`), not in `PipelineExecutor`.
  - Add a focused “large variable boundaries” subsection (from current runtime behavior):
    - Bash/sh parameter adaptation path: `adaptVariablesForBash()` resolves values to strings before shell execution (`interpreter/env/bash-variable-adapter.ts`).
    - Oversized bash/sh values are moved from exported env into heredoc-injected shell-local assignments in `BashExecutor` (prelude injection before script execution).
    - Heredoc safety behaviors to document: variable-name sanitization, unique marker collision checks, and non-exported oversized values.
    - `/run` simple command auto-fallback for oversized payloads is handled by `CommandExecutorFactory.executeCommand()` (fallback to `BashExecutor` unless `MLLD_DISABLE_SH` opt-out).
    - Shell-size guard thresholds for fallback checks are driven by `MLLD_MAX_SHELL_ENV_VAR_SIZE`, `MLLD_MAX_SHELL_ENV_TOTAL_SIZE`, `MLLD_MAX_SHELL_COMMAND_SIZE`, `MLLD_MAX_SHELL_ARGS_ENV_TOTAL`.
  - Keep threshold values aligned with code defaults:
    - Bash heredoc threshold currently defaults to `64 * 1024` when unset in `BashExecutor` (`MLLD_MAX_BASH_ENV_VAR_SIZE`).
    - `/run` fallback pre-check defaults are ~128KB per env var, ~200KB env total, ~128KB command, ~256KB combined args+env in `CommandExecutorFactory`/`ShellCommandExecutor`.
  - Clarify debug knobs without hard-coding wrong stream assumptions:
    - `MLLD_DEBUG` and `MLLD_DEBUG_BASH_SCRIPT` emit heredoc/script diagnostics in bash executor paths.

- `docs/dev/LARGE-VARIABLES.md`
  - After the above merge into `DATA.md`, this file is ready for deletion.

- Do NOT carry these stale claims into `DATA.md`:
  - `MLLD_MAX_BASH_ENV_VAR_SIZE` default is 131072 (current default in `BashExecutor` is 64KB when unset).
  - Adapter behavior implying temp-file based large-value handling or special LoadContentResult array blank-line joining; current adapter always stringifies and returns `tempFiles: []`.
  - “debug script dump goes to stderr” as a hard rule; current debug output uses mixed console/error and direct stdout writes.
