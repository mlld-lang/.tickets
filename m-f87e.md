---
id: m-f87e
status: closed
deps: []
links: [m-fa7e, m-2541]
created: 2026-03-05T19:47:10Z
type: feature
priority: 0
assignee: Adam
tags: [checkpoint, vfs, box]
updated: 2026-03-06T10:27:06Z
---
# Checkpoint resume should snapshot and restore VFS workspace state

## Problem

Checkpoint resume caches directive return values but not VFS workspace side effects.
When an LLM call inside a box writes files to the VFS via tools (Write, Bash), those
writes are not replayed on resume. If a later section reads from the workspace, the
file doesn't exist.

Example:
```mlld
## Section 1
/var @ws = box [
  let @dummy = @claude("Write output.txt with 'hello'", { model: "haiku", tools: ["Write"] })
]

---

## Section 2
/show <@ws/output.txt>   >> fails on resume — output.txt was never written
```

Fresh run: works. Resume: Section 1 replays the cached @claude return value, but the
Write tool call that created output.txt in the VFS never executes. Section 2 finds
an empty workspace.

## Solution

Snapshot VFS state at each checkpoint boundary (section `---` in markdown mode).

### What to snapshot

VirtualFS is three serializable structures:
- `shadowFiles` — Map<string, string> (file path → content)
- `deletedPaths` — Set<string>
- `explicitDirectories` — Set<string>

### Checkpoint serialize (after section completes)

If there are active workspaces, export their VFS state and store alongside the
section's cached result in the checkpoint JSON. Something like:

```json
{
  "sectionResult": "...",
  "workspaceSnapshots": [
    {
      "shadowFiles": { "/output.txt": "hello" },
      "deletedPaths": [],
      "explicitDirectories": []
    }
  ]
}
```

### Checkpoint deserialize (before next section on resume)

If the checkpoint has workspace snapshots, restore them:
- If the workspace already exists (box is still in scope), apply the snapshot to its VFS
- If there is no active workspace, do nothing (no-op)

### Edge cases to consider

- **Primary path**: Snapshot exists and an active/bound workspace exists; restore must make `<@ws/...>` reads identical to a fresh run.
- **Shell correctness**: Invalidate stale `shellSession` on restore so later commands operate on restored VFS state.
- **Malformed snapshot**: Fail soft (warn + continue), never crash execution.
- **No active workspace**: Replay should no-op safely.

## Implementation details (v1 scope)

### 1) Extend checkpoint payloads to carry workspace snapshots

Update checkpoint storage so each cached operation can also persist workspace side effects:

- **`interpreter/checkpoint/CheckpointManager.ts`**
  - Extend `CheckpointPutEntry` with optional `workspaceSnapshot`.
  - Extend `StoredResultEnvelope` to include optional `workspaceSnapshot`.
  - Add a typed read API (for example `getWithMetadata`) that returns both cached `value` and optional snapshot.
  - No legacy checkpoint resume compatibility requirement for this ticket.

### 2) Add serializable workspace snapshot model

Define a minimal snapshot format derived from the currently active `WorkspaceValue`:

- `vfsPatch` (`workspace.fs.export()` output)
- `descriptions` (object map converted from `Map<string,string>`)

Files:

- **`core/types/workspace.ts`**
  - Add snapshot interfaces/types for checkpoint persistence.

### 3) Capture snapshots on checkpoint miss writes

When checkpoint post-hook writes a new cache entry, capture workspace state from the current runtime context:

- **`interpreter/hooks/checkpoint-post-hook.ts`**
  - On miss path (before `manager.put`), read `env.getActiveWorkspace()`.
  - If workspace exists, capture one snapshot and include it in `CheckpointPutEntry`.
  - If no active workspace, store no snapshot.

This ensures cache entries represent both return value and VFS side effects produced by that operation.

### 4) Restore snapshots on checkpoint hits

When a cached result is used, rehydrate workspace side effects before the caller continues:

- **`interpreter/hooks/checkpoint-pre-hook.ts`**
  - Read checkpoint envelope metadata (value + `workspaceSnapshot`) and attach snapshot to hook metadata.
- **`interpreter/hooks/checkpoint-post-hook.ts`**
  - On hit path:
    - if snapshot exists and `env.getActiveWorkspace()` exists, apply snapshot to that workspace.
    - if no active workspace, do nothing (no-op).
  - Reset `shellSession` when rehydrating (force lazy recreation against restored VFS state).
  - Fail-soft on malformed snapshot or apply errors: log warning and continue execution.

### 5) Section-boundary behavior

The user-visible guarantee should be section correctness: any section after a cached tool call must observe the same workspace files as a fresh run.

Implementation note: current checkpoint infrastructure is operation-centric, so persistence/restore should attach to each checkpointed operation; this still satisfies section-boundary correctness because section outputs depend on those operations.

## Explicit non-goals (v1)

- Restore by guessed stack position when no bound/current workspace exists.
- Create and activate new workspaces during checkpoint replay.
- Nested workspace restore semantics.
- Workspace alias graph handling (`@a = @ws` and similar).
- Fork-cache-specific behavior.
- Legacy checkpoint resume compatibility handling.

## Tests to add

Per `docs/dev/TESTS.md`, add both focused unit coverage and scenario-level integration coverage.

### Unit tests

- **`tests/interpreter/checkpoint/CheckpointManager.test.ts`**
  - persists and reloads `workspaceSnapshot` in result envelopes.

- **`tests/interpreter/checkpoint/checkpoint-runtime-semantics.test.ts`**
  - `box` workspace + checkpointed llm-labeled operation writes `/output.txt`; second run hits cache and still reads `/output.txt`.
  - no active workspace on replay is a no-op (no crash, no restore attempt).
  - malformed snapshot restore degrades gracefully (warning + no crash).

### Integration fixture

Add fixture directory:

- **`tests/cases/integration/checkpoint/workspace-vfs-resume/`**
  - `example.md`: first section writes file through cached op inside box; second section reads the file.
  - `expected.md`: proves second section succeeds on both cold run and resume run.

Wire fixture into:

- **`tests/interpreter/checkpoint/integration-fixtures.test.ts`**
  - run once with `fresh: true`, then with `resume: true`, assert identical output and expected hit behavior.

## Acceptance criteria

- Resume hit preserves workspace file side effects from cached operations.
- `<@ws/...>` reads after a resumed section return the same content as fresh run.
- Stale `shellSession` is invalidated when workspace snapshot restore occurs.
- Malformed snapshot data does not crash execution.
- No active workspace during replay results in a safe no-op.
- New tests above pass reliably.

## Related

- m-fa7e: Checkpoint auto-resume UX (makes resume opt-in, reducing accidental hits)
- This ticket addresses the correctness issue when resume IS used intentionally
