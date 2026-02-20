---
id: m-9bab
status: open
deps: [m-5870, m-3f0b, m-fd36, m-a3fe]
created: 2026-02-20T01:13:10Z
type: task
priority: 2
assignee: Adam Avenir
updated: 2026-02-20T01:13:25Z
---
# Migrate orchestrator output to llm/output/

## Problem

Each orchestrator writes output to a different location:
- review: runs/review-YYYY-MM-DD/
- qa: qa/runs/YYYY-MM-DD-N/
- polish: pipeline/YYYY-MM-DD-N/
- j2bd: j2bd/<topic>/runs/YYYY-MM-DD-N/
- doc-audit: runs/doc-audit-YYYY-MM-DD/

Convention is to standardize on llm/output/<name>-YYYY-MM-DD-N/ (gitignored).

## Work

1. Update each orchestrator to use @resolveOutputDir from @lib/runs
2. Output goes to llm/output/<name>-YYYY-MM-DD-N/
3. Names: review-<config-name>, qa, polish, j2bd-<topic>, doc-audit, doc-writer
4. Update .gitignore entries (llm/output/ already added, can remove old entries)
5. Leave existing run data in old locations (don't move old runs)

See llm/CONVENTIONS.md for the @output/ path alias.

