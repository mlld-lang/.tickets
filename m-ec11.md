---
id: m-ec11
status: closed
deps: []
links: []
created: 2026-02-06T20:17:49Z
type: chore
priority: 2
assignee: Adam Avenir
tags: [docs, when, dx]
---
# when match form : syntax unclear in docs

howto when shows `when @mode: [` for the local-vars-in-match form, which implies ALL match forms need `:`. They don't — only when you have let declarations before branches. The plain match form `when @val [...]` works without `:`. The docs should clarify when `:\ is required (local vars) vs not needed (plain match).


## Notes

**2026-02-06T20:39:48Z**

Fixed: colon is now optional in match form. `when @val [...]` works without colon. Docs updated to remove colon from all examples. Atoms and LLM docs rebuilt.
