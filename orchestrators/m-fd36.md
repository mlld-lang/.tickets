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

Run directory management helpers are duplicated across 3 scripts with identical or near-identical logic.

## Duplications

- @today(): qa/lib/state.mld, polish/lib/state.mld (identical)
- @countRunsForDate(): qa/lib/state.mld, polish/lib/state.mld, j2bd/index.mld (identical)
- @getLatestRun(): qa/lib/state.mld, polish/lib/state.mld (identical)
- @nextRunId(): qa/lib/state.mld, polish/lib/state.mld, j2bd/index.mld (3 variants, same logic)

## Work

1. Create llm/lib/runs.mld with @today, @countRunsForDate, @getLatestRun, @nextRunId
2. Add @resolveOutputDir(name, runId) that standardizes on llm/output/<name>-YYYY-MM-DD-N/
3. Export all helpers
4. Update orchestrators to import from @lib/runs

See llm/CONVENTIONS.md for the output directory naming convention.

