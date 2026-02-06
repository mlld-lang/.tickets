---
id: m-da13
status: closed
deps: []
created: 2026-02-05T13:06:49Z
type: task
priority: 2
assignee: Adam
tags: [phase-2, artifact]
updated: 2026-02-05T13:13:37Z
---
# Create file-taint-demo.mld demonstrating audit ledger and taint inheritance

## Goal
Create j2bd/security/artifacts/file-taint-demo.mld demonstrating the audit ledger and file taint inheritance.

## Requirements
The script must:
1. Write a labeled value (e.g., `var secret @token = "..."`) to a file using `output @token to "demo.txt"`
2. Read the file back using `var @loaded = <demo.txt>`
3. Show that `@loaded.mx.labels` contains the original label (`secret`)
4. Show how to query `.mlld/sec/audit.jsonl` for the corresponding write event

## Implementation notes
- Use the pattern from label-tracking.md atom
- The audit log stores `write` events with `path`, `taint`, and `writer` fields
- After writing, use `run cmd { jq 'select(.event=="write") | select(.path | endswith("demo.txt"))' .mlld/sec/audit.jsonl }` to query
- The script should be self-documenting with comments explaining each step
- Output should clearly show the taint inheritance working

## Validation
Run `mlld validate j2bd/security/artifacts/file-taint-demo.mld` to check syntax.
Run `mlld run j2bd/security/artifacts/file-taint-demo.mld` to verify the demo works.

