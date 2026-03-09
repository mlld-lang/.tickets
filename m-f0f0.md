---
id: m-f0f0
status: closed
deps: []
links: []
created: 2026-03-04T16:54:00Z
type: task
priority: 2
assignee: Adam
updated: 2026-03-04T17:05:19Z
---
# BUG: run sh inside box bypasses VFS entirely — reads/writes hit real filesystem. run cmd correctly routes through ShellSession/VFS, but run sh does not. This means sh blocks inside a box can read real FS files and write to real FS, breaking box isolation. Repro: box [ run sh { echo leaked > /tmp/test.txt } ] writes to real /tmp/test.txt.

