---
id: m-a1af
status: closed
deps: []
links: []
created: 2026-03-04T16:39:08Z
type: task
priority: 2
assignee: Adam
updated: 2026-03-04T18:00:10Z
---
# Fix existing box tests: all 8 pre-existing tests use @root inside cmd {} which hits real FS not VFS. Tests pass only because the test runner uses MemoryFileSystem as backing FS. Tests: anonymous-vfs-box, cmd-workspace-parameter, config-fs-workspace-vfs, nested-workspace-stack, resolver-shorthand-vfs, cmd-path-backward-compatible. Should use relative paths inside box shell commands instead.

