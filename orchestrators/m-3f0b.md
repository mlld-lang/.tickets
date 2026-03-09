---
id: m-3f0b
status: open
deps: []
created: 2026-02-20T01:12:55Z
type: task
priority: 2
assignee: Adam Avenir
---
# Create publishable `@mlld/event-log` module

## Problem

Append-only `events.jsonl` logging is now a real orchestration primitive, not just a pre-checkpoint resume workaround.

Checkpoint/resume replaced the old custom run-state machinery, but several orchestrators still rely on event logs for:

- workflow memory (`what already happened in this run?`)
- coordination (`what still needs merge / docs / follow-up?`)
- agent context (`recent events` fed back into a decision prompt)
- audit/debugging (`why did this run pause or fail?`)

The core event-log IO is duplicated today:

- QA has local `logEvent/loadEvents` helpers
- Polish has the same core helpers plus Polish-specific workflow queries
- J2BD has its own `logEvent` and `loadRecentEvents`
- qatest2 has another thin `logEvent`

What should be shared is the **event-log primitive**, not a single orchestrator-wide event schema.

## Source implementations

**QA events** (`llm/run/qa/lib/events.mld`)
- `@logEvent(runDir, event)` appends `{ ts: @now, ...@event }` to `events.jsonl`
- `@loadEvents(runDir)` reads the full file
- wraps those with QA-specific helpers like `@logPhaseStart` / `@logItemDone`

**Polish events** (`llm/run/polish/lib/events.mld`)
- same core `@logEvent` / `@loadEvents`
- adds Polish-only wrappers and query helpers:
  `@logBatchStart`, `@logBatchDone`, `@logMergeDone`, `@logMergeFailed`,
  `@logManualReview`, `@getCompletedItems`, `@getExecuteCompletedItems`,
  `@getVerifiedNotMerged`

**J2BD**
- `llm/run/j2bd/lib/questions.mld` has a separate `@logEvent(runDir, eventType, data)`
- `llm/run/j2bd/lib/context.mld` has `@loadRecentEvents(runDir, limit)`
- event names are J2BD-specific (`iteration`, `worker_result`, `run_paused`, `job_complete`)

**qatest2**
- `llm/run/qatest2/lib/context.mld` has another thin `@logEvent`

## Required change

### Step 1: Create `modules/event-log/`

Create a publishable library module:

```text
modules/event-log/
├── index.mld
├── module.yml
└── README.md
```

This module will be published as `@mlld/event-log`.

`module.yml` should use normal module packaging metadata for a public library module.

### Step 2: Shared API surface

Export only the cross-orchestrator primitives:

- `@logEvent(runDir, event)`
- `@loadEvents(runDir)`
- `@loadRecentEvents(runDir, limit)`

Expected behavior:

- `@logEvent(runDir, event)`
  - accepts a full event object, not `(eventType, data)`
  - writes `{ ts: @now, ...@event }` to `@runDir/events.jsonl`
  - returns the event object that was written
- `@loadEvents(runDir)`
  - returns parsed JSONL entries in file order
  - returns `[]` if the file does not exist
- `@loadRecentEvents(runDir, limit)`
  - returns the last `limit` parsed events
  - returns `[]` if the file does not exist
  - can be implemented via `@loadEvents(...).slice(...)` unless there is a strong reason to optimize around `tail`

Do **not** put orchestrator-specific event names or workflow query helpers in this shared module.

### Step 3: Update QA to consume the module

Refactor `llm/run/qa/lib/events.mld` so it imports the shared primitives from the module and keeps only QA-specific wrappers:

```mlld
import {
  @logEvent,
  @loadEvents
} from <@root/modules/event-log/index.mld>
```

Keep local QA helpers like:

- `@logPhaseStart`
- `@logPhaseComplete`
- `@logItemStart`
- `@logItemDone`

Delete duplicated core implementations from QA's local file.

### Step 4: Update Polish to consume the module

Refactor `llm/run/polish/lib/events.mld` so it imports:

```mlld
import {
  @logEvent,
  @loadEvents,
  @loadRecentEvents
} from <@root/modules/event-log/index.mld>
```

Keep Polish-specific wrappers and query helpers local:

- phase/item/batch logging wrappers
- merge/manual-review wrappers
- `@getCompletedItems`
- `@getExecuteCompletedItems`
- `@getVerifiedNotMerged`

Polish phases should continue importing from `./lib/events.mld`; that file becomes the Polish adapter layer on top of the shared module.

### Step 5: Optionally migrate J2BD / qatest2 in the same ticket if low-friction

If the migration is straightforward, update:

- `llm/run/j2bd/lib/questions.mld`
- `llm/run/j2bd/lib/context.mld`
- `llm/run/qatest2/lib/context.mld`

to consume the shared primitives.

Important: J2BD's event schema should remain J2BD-specific. Only the file IO helpers should be shared.

If this expands scope too much, split J2BD/qatest2 into a follow-up.

### Step 6: Publish intent

The module should be structured so it can be published as:

```mlld
import { @logEvent, @loadEvents, @loadRecentEvents } from @mlld/event-log
```

Repo-local consumers may use `<@root/modules/event-log/index.mld>` until the module is actually published.

## Acceptance criteria

- `modules/event-log/` exists with `index.mld`, `module.yml`, and `README.md`
- the module exports exactly the shared event-log primitives:
  - `@logEvent`
  - `@loadEvents`
  - `@loadRecentEvents`
- QA consumes the shared primitive module and keeps only QA-specific wrappers locally
- Polish consumes the shared primitive module and keeps only Polish-specific wrappers/query helpers locally
- no orchestrator-specific event taxonomy is forced into the shared module
- local consumers have a clear migration path to `@mlld/event-log`

## Non-goals

- Standardizing all orchestrators on the same event names
- Moving Polish workflow query helpers into the shared module
- Replacing checkpoint/resume
- Reintroducing old run-state files like `run.json`
