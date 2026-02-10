---
id: m-fad3
status: closed
deps: []
links: []
created: 2026-02-06T20:17:43Z
type: chore
priority: 2
assignee: Adam Avenir
tags: [dx, when, if, docs]
---
# when vs if block syntax inconsistency

if @cond [block] works without =>, but when @cond [block] requires =>: when @cond => [block]. This is confusing because they look similar. Users (and LLMs) naturally write `when @exists == "yes" [block]` by analogy with `if @exists [block]`. Consider either: (a) making when accept the same form, or (b) documenting the distinction more prominently in howto.


## Notes

**2026-02-06T20:39:51Z**

Fixed: `when @expr [conditions]` now works as the match form (no colon or arrow needed). Grammar updated with bare form alternative in WhenMatchForm. All when/if error messages now include educational cheat sheet showing when vs if semantics and return behavior differences.
