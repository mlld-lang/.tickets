---
id: m-b71c
status: closed
deps: []
created: 2026-02-05T13:01:54Z
type: task
priority: 2
assignee: Adam
tags: [phase-1, documentation]
updated: 2026-02-05T13:05:12Z
---
# Expand label-tracking atom for file I/O taint

The label-tracking atom currently only has 5 bullet points:
- Method calls
- Templates
- Field access
- Iterators
- Pipelines

It does NOT cover the crucial file I/O taint inheritance behavior that this job requires.

**Required content:**
1. Explain that when a labeled value is written to disk (via output/append), the audit log records the taint
2. Explain that when the file is read back, the taint is restored from the audit log
3. Show a working example demonstrating write -> read -> labels preserved
4. Reference the audit-log atom for the underlying mechanism

**Target example from job:**
```mlld
var secret @token = "sk-live-123"
output @token to "demo.txt"

var @loaded = <demo.txt>
show @loaded.mx.labels  // Should show ["secret"]
```

**Acceptance criteria:**
- Atom is 100-200 words (currently ~20)
- Includes working, validated mlld example
- Explains the file I/O taint inheritance mechanism
- References audit-log for the underlying ledger

