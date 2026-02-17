---
id: m-e1e1
status: closed
deps: []
created: 2026-02-15T16:48:31Z
type: bug
priority: 1
assignee: Adam
tags: [qa-2026-02-14, p1, cmd, escaping]
updated: 2026-02-15T17:00:03Z
---
# Enable @ escaping in cmd {} blocks

Currently cmd {} blocks don't support @ escaping - both @@ and \@ are passed verbatim to the shell. This means you cannot have a literal @ in a cmd block that doesn't trigger variable interpolation.

Example that should work:
cmd { echo "email@@example.com" }  # should output email@example.com

Currently both @@ and \@ are passed literally to shell. This is likely what QA agents found when they flagged @@ as broken.

Fix: make cmd {} blocks respect EscapeSequence like other string contexts.

## Acceptance Criteria

cmd { echo "user@@domain.com" } outputs user@domain.com

