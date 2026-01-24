---
id: mlld-itqe
status: closed
deps: []
links: []
created: 2026-01-10T23:41:27.928762-08:00
type: bug
priority: 2
---
# Intro example uses incorrect glob pattern

The intro.md alligator glob example uses `<docs/*.md>` but there are no .md files directly in docs/ - they're all in subdirectories like docs/user/, docs/dev/, etc.

Current example (returns 0 files):
```mlld
var @docs = <docs/*.md>
for @doc in @docs => show @doc.mx.filename
```

Should be:
```mlld
var @docs = <docs/user/*.md>
for @doc in @docs => show @doc.mx.filename
```

Or use recursive glob:
```mlld
var @docs = <docs/**/*.md>
```

Location: docs/user/introduction.md around line 435


