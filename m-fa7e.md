---
id: m-fa7e
status: closed
deps: []
links: [m-f87e]
created: 2026-03-05T18:49:28Z
type: bug
priority: 0
assignee: Adam
tags: [checkpoint, ux]
updated: 2026-03-05T19:47:13Z
---
# Checkpoint auto-resume is silently confusing; clean command path matching broken

## Problem

The checkpoint system auto-resumes by default when re-running a file that has a prior checkpoint. This caused real debugging confusion during QA: after fixing a bug and re-running `mlld tmp/qa-claude-v3.mld.md`, stale results from the pre-fix run were silently replayed. There was no indication that cached results were being used, so it appeared the fix hadn't worked.

Additionally, `mlld checkpoint clean tmp/qa-claude-v3.mld.md` reports "No checkpoint cache found" even though the cache exists at `.mlld/checkpoints/qa-claude-v3`. The clean command doesn't match on the full file path — it uses a different key than the checkpoint system uses for storage.

## What I'd want as a user

1. **Don't auto-resume by default.** When I run `mlld file.mld.md`, I expect a fresh run. If a checkpoint exists, show a message like:
   ```
   Checkpoint found for this file (3 of 12 sections cached).
   Use --resume to continue from where you left off.
   ```
   Then proceed with a fresh run. Auto-resume should be opt-in via `--resume` or similar flag.

2. **Preserve past checkpoints for resumability.** Before changing the default, verify that switching to opt-in resume doesn't cause old checkpoints to be wiped on a fresh run. If the current auto-resume exists because a fresh run would overwrite the checkpoint data, the implementation needs to keep the old checkpoint intact until the user explicitly resumes or the new run progresses past the cached point. Users should be able to `--resume` at any time without losing their prior progress.

3. **Fix checkpoint clean path matching.** `mlld checkpoint clean tmp/qa-claude-v3.mld.md` should find and clean `.mlld/checkpoints/qa-claude-v3`. The clean command should accept either the file path or the checkpoint key, and resolve both to the same cache location.

## Reproduction

```bash
# Run a file (creates checkpoint)
mlld tmp/qa-claude-v3.mld.md

# Make changes to imported modules
# Re-run — stale results silently replayed
mlld tmp/qa-claude-v3.mld.md

# Try to clean — doesn't find it
mlld checkpoint clean tmp/qa-claude-v3.mld.md
# "No checkpoint cache found"

# Actual cache location
ls .mlld/checkpoints/qa-claude-v3
# exists!

# Manual workaround
rm -rf .mlld/checkpoints/qa-claude-v3
```

