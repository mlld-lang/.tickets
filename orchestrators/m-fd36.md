---
id: m-fd36
status: open
deps: []
created: 2026-02-20T01:13:00Z
type: task
priority: 2
assignee: Adam Avenir
---
# Create llm/lib/runs.mld shared library

## Problem

Run directory management is duplicated across orchestrators. QA has the most complex implementation (`llm/run/qa/index.mld:44-73`) with auto-increment and resume detection. Polish, PR-Review, and others have simpler inline versions.

## Current implementations

**QA** (`llm/run/qa/index.mld:44-73`):
- `var @today = @now.slice(0, 10)` — date formatting
- `@nextIteration(qaRoot, today, n)` — recursive counter to find next slot
- `@nextCompleteIteration(qaRoot, today, n)` — same but checks completion marker
- `@findLastIncomplete(qaRoot, today)` — shell: finds most recent dir without completion marker
- `@resolveRunDir(qaRoot, today, forceNew)` — orchestrates: resume incomplete or create new

**QA state** (`llm/run/qa/lib/state.mld`) — reduced to just `var @qaRoot = "@base/qa/runs"` (legacy; run dir helpers were moved inline to index.mld).

**Polish** (`llm/run/polish/index.mld:55`) — fixed path `@base/pipeline/current-run`, no dated suffix (checkpoint handles resumption).

**PR-Review** (`llm/run/pr-review/index.mld:50-51`) — `var @runDate = @now.slice(0, 10)` + `var @outDir = "@outBase/@runDate"`.

## Required change

### Step 1: Create `llm/lib/runs.mld`

The shared library provides a standardized `@output/` directory convention per `llm/CONVENTIONS.md`. The target pattern is `llm/output/<name>-YYYY-MM-DD-N/`.

```mlld
>> Run directory management
>> Import: import { @today, @nextRunId, @resolveOutputDir, @getLatestRun } from @lib/runs

needs { sh }

>> Returns YYYY-MM-DD
exe @today() = @now.slice(0, 10)

>> Count existing runs for a name+date prefix in outputRoot
exe @countRunsForDate(outputRoot, prefix) = sh {
  ls -1d "$outputRoot"/${prefix}-* 2>/dev/null | wc -l | tr -d ' '
}

>> Returns "<name>-YYYY-MM-DD-N" (the run ID, not the full path)
exe @nextRunId(outputRoot, name) = [
  let @date = @today()
  let @prefix = `@name-@date`
  let @count = @countRunsForDate(@outputRoot, @prefix)
  let @n = @count ? @count * 1 : 0
  => `@prefix-@n`
]

>> Find the latest run directory for a name prefix
exe @getLatestRun(outputRoot, name) = sh {
  ls -1d "$outputRoot"/${name}-* 2>/dev/null | sort -r | head -1
}

>> Resolve or create a run directory under @output/
>> runId: "latest" to resume latest, specific ID to resume, or omit/empty for new
exe @resolveOutputDir(name, runId) = [
  let @outputRoot = "@base/llm/output"
  sh { mkdir -p "$outputRoot" }

  >> Explicit run ID — resume specific
  when @runId && @runId != "latest" && @runId != "" => [
    let @dir = `@outputRoot/@runId`
    sh { mkdir -p "$dir" }
    => @dir
  ]

  >> "latest" — resume most recent
  when @runId == "latest" => [
    let @latest = @getLatestRun(@outputRoot, @name)
    let @trimmed = @latest.trim()
    when @trimmed => @trimmed
    >> No existing run — create new
    let @newId = @nextRunId(@outputRoot, @name)
    let @dir = `@outputRoot/@newId`
    sh { mkdir -p "$dir" }
    => @dir
  ]

  >> Default — create new
  let @newId = @nextRunId(@outputRoot, @name)
  let @dir = `@outputRoot/@newId`
  sh { mkdir -p "$dir" }
  => @dir
]

/export { @today, @nextRunId, @resolveOutputDir, @getLatestRun }
```

### Step 2: Do NOT update existing orchestrators yet

This ticket only creates the library. The migration of existing orchestrators to use `@resolveOutputDir` is handled by m-9bab (Migrate orchestrator output to `llm/output/`). QA's custom run dir logic (`@findLastIncomplete`, completion markers) is specific to QA and stays in QA — the shared library provides the convention, not every QA-specific pattern.

## Acceptance criteria

- `llm/lib/runs.mld` exists with `@today`, `@nextRunId`, `@resolveOutputDir`, `@getLatestRun` exported.
- `@resolveOutputDir("qa")` creates `llm/output/qa-YYYY-MM-DD-0/` (or increments N).
- `@resolveOutputDir("qa", "latest")` returns the most recent `qa-*` directory.
- `@resolveOutputDir("qa", "qa-2026-02-24-0")` returns that specific directory.
- The `@output/` path alias in `mlld-config.json` resolves to `llm/output/` (verify it exists).

