---
id: m-ab85
status: open
deps: []
links: []
created: 2026-03-04T18:00:37Z
type: chore
priority: 2
assignee: Adam
tags: [box, docs]
---
# Document box net restriction behavior

QA finding: box net: { allow: [] } config only restricts mlld-level network operations (provider API calls), not shell commands. Shell commands in VFS boxes already can't access system binaries (curl, wget, etc.) because the just-bash interpreter doesn't have access to /usr/bin/. Also: the with-clause grammar doesn't support net - it's only available in the config object form. This should be documented in the box reference.

