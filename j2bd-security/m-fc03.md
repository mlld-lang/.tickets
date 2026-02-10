---
id: m-fc03
status: closed
deps: []
created: 2026-02-09T15:03:05Z
type: task
priority: 2
assignee: Adam Avenir
tags: [urgency-high, excellence, polish]
updated: 2026-02-09T15:10:15Z
---
# Fix non-runnable pattern atom examples + polish gaps

Excellence assessment found both capstone pattern atoms fail when run. This is the highest-impact polish gap preventing 'excellent' rating.

## Critical Fix: Non-runnable examples

### pattern-dual-audit.md (lines 55-57)
Change mock LLMs from `exe llm` to plain `exe` to avoid enforcement guard conflict:
- Line 56: `exe llm @extract(input)` → `exe @extract(input)`
- Line 57: `exe llm @decide(summary, policy)` → `exe @decide(summary, policy)`
- Line 55 comment already says 'Mock LLMs' — update to: `>> Mock exes (in production, use exe llm for autoverify)`

### pattern-audit-guard.md (lines 42-44)
Same fix:
- Line 43: `exe llm @process(data)` → `exe @process(data)`
- Line 44: `exe llm @audit(content)` → `exe @audit(content)`  
- Line 42 comment: update to `>> Mock exes (in production, use exe llm for autoverify)`

Both atoms already reference main.mld in related-code and in notes for the full working example.

## Additional Polish

### Add reading-order note to pattern-dual-audit.md (before Notes section)
Add a line: `**Prerequisites:** Read `sign-verify` → `autosign-autoverify` → `labels-influenced` → `guards-privileged` → `pattern-audit-guard` before this atom.`

### Add retry-vs-deny note to both pattern atoms
The enforcement guard in atoms uses `retry` while main.mld uses `deny`. Add brief note explaining: retry = MCP mode (LLM gets another chance), deny = standalone mode (immediate block).

## Validation
After fixes, run both atom examples:
- `mlld tmp/excellence-dual-audit-atom.mld` (rebuild from atom)
- `mlld tmp/excellence-audit-guard-atom.mld` (rebuild from atom)
Both should complete without 'Guard retry limit exceeded' errors.


**2026-02-09 15:10 UTC:** Worker verified: pattern-dual-audit outputs 'Audit rejected: destructive action requested', pattern-audit-guard outputs '{"approved": false, "reason": "injection detected"}'. Both validate cleanly.
