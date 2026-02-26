---
id: m-3f0b
status: open
deps: []
created: 2026-02-20T01:12:55Z
type: task
priority: 2
assignee: Adam Avenir
---
# Create llm/lib/events.mld shared library

## Problem

Event logging is duplicated across orchestrators. QA (`llm/run/qa/lib/events.mld`) and Polish (`llm/run/polish/lib/events.mld`) have nearly identical core functions. Other orchestrators have inline event logging with a different signature.

## Source implementations

**QA events** (`llm/run/qa/lib/events.mld`) — 44 lines, exports 6 functions:
- `@logEvent(runDir, event)` — core: appends `{ ts: @now, ...@event }` to `events.jsonl`
- `@logPhaseStart(runDir, phase)`
- `@logPhaseComplete(runDir, phase, extra)`
- `@logItemStart(runDir, id, itemType)`
- `@logItemDone(runDir, id, status, extra)`
- `@loadEvents(runDir)`

**Polish events** (`llm/run/polish/lib/events.mld`) — 126 lines, identical core + 9 extra functions:
- Same 6 core functions (identical to QA)
- Polish-specific extensions: `@logBatchStart`, `@logBatchDone`, `@logMergeDone`, `@logMergeFailed`, `@logManualReview`, `@getCompletedItems`, `@getExecuteCompletedItems`, `@getVerifiedNotMerged`

## Required change

### Step 1: Create `llm/lib/events.mld`

Copy `llm/run/qa/lib/events.mld` to `llm/lib/events.mld`. This becomes the shared core with the 6 exported functions. The file is already well-structured — no changes needed to the code itself, just the new location.

### Step 2: Update QA

In `llm/run/qa/index.mld`, the existing import line is:
```mlld
>> (QA imports events from its own lib — find the import or inline usage)
```

Change QA's events import to `@lib/events`:
```mlld
import { @logEvent, @logPhaseStart, @logPhaseComplete, @logItemStart, @logItemDone, @loadEvents } from @lib/events
```

Delete `llm/run/qa/lib/events.mld` after confirming QA works with the shared import.

### Step 3: Update Polish

Replace Polish's events.mld with a file that imports the shared core and adds its extensions:

```mlld
>> Events Library (Polish extensions)
>> Core logging imported from @lib/events, Polish-specific extensions below

import { @logEvent, @logPhaseStart, @logPhaseComplete, @logItemStart, @logItemDone, @loadEvents } from @lib/events

>> Polish-specific extensions below (batch, merge, manual review, query helpers)
exe @logBatchStart(runDir, count) = [
  => @logEvent(@runDir, { event: "batch_start", count: @count })
]
>> ... (keep all Polish-specific functions as-is)

/export {
  @logEvent, @logPhaseStart, @logPhaseComplete, @logItemStart, @logItemDone, @loadEvents,
  @logBatchStart, @logBatchDone, @logMergeDone, @logMergeFailed, @logManualReview,
  @getCompletedItems, @getExecuteCompletedItems, @getVerifiedNotMerged
}
```

Polish's phases continue importing from `./lib/events.mld` — they get the shared core re-exported plus Polish extensions.

### Step 4: Other orchestrators

Other orchestrators (review, doc-audit, j2bd, qatest2) have inline event logging with different signatures. Do NOT update them in this ticket — they can be migrated individually later or as part of m-9bab.

## Acceptance criteria

- `llm/lib/events.mld` exists with 6 core functions exported.
- QA imports events from `@lib/events` (not local `./lib/events.mld`).
- Polish's `lib/events.mld` imports core from `@lib/events` and adds its extensions.
- QA and Polish orchestrators still function correctly.

