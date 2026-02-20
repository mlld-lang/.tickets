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

@logEvent has 3 different implementations across 5 scripts. @logPhaseStart, @logPhaseComplete, @logItemStart, @logItemDone are duplicated between qa and polish.

## Variants

- Variant A (runDir, eventType, data): review, doc-audit, review-docs, j2bd — separate eventType arg
- Variant B (runDir, event): qa, polish — single event object with .event key
- @logPhaseStart/@logPhaseComplete/@logItemStart/@logItemDone: identical in qa/lib/events.mld and polish/lib/events.mld

## Work

1. Create llm/lib/events.mld with unified @logEvent signature
2. Include @logPhaseStart, @logPhaseComplete, @logItemStart, @logItemDone
3. Update all orchestrators to import from @lib/events
4. Remove local event logging duplicates

