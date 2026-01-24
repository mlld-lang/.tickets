---
id: mlld-wmzl.3
status: closed
deps: [mlld-wmzl.2]
links: []
created: 2026-01-16T07:18:01.377328-08:00
type: task
priority: 0
parent: mlld-wmzl
---
# Phase 3: op:* auto-applied operation labels

## Goal

Auto-apply op:* labels for all operations and record operation provenance without propagating op labels into data labels.

## Operation Label Mapping

| Directive | Auto-Applied Labels |
|-----------|---------------------|
| `run cmd { git status }` | `op:cmd`, `op:cmd:git`, `op:cmd:git:status` |
| `run sh { ... }` | `op:sh` |
| `run node { ... }` | `op:node` |
| `run js { ... }` | `op:js` |
| `run py { ... }` | `op:py` |
| `run prose { ... }` | `op:prose` |
| `/show` | `op:show` |
| `/output` | `op:output` |
| `/log` | `op:log` |
| `/append` | `op:append` |
| `/stream` | `op:stream` |

## Implementation

- `core/policy/operation-labels.ts` provides `getOperationLabels` and `parseCommand` (shell-quote aware).
- `OperationContext.opLabels` stores labels for policy checks and guard filters.
- `OperationContext.sources` stores provenance (e.g., `cmd:git:status`).
- `taint-post-hook.ts` records sources only; op labels do not propagate into `SecurityDescriptor.labels` or `taint`.

## Integration Points

Apply labels and sources in:
- `interpreter/eval/directive.ts` operation context
- `interpreter/eval/run.ts` for command parsing
- `interpreter/eval/show.ts`, `output.ts`, `append.ts`, `stream.ts`, `log` (if present)
- exec and pipeline contexts that emit effects

## Exit Criteria

- [ ] `getOperationLabels()` returns hierarchical labels
- [ ] `parseCommand()` extracts command and subcommand
- [ ] Operation contexts include opLabels and sources
- [ ] Output metadata carries sources, not op labels
- [ ] Tests verify label hierarchy and matching



