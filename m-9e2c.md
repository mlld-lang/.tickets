---
id: m-9e2c
status: closed
deps: []
created: 2026-02-15T20:35:52Z
type: task
priority: 2
assignee: Adam
updated: 2026-02-15T22:52:32Z
---
# Enhance policy-auth.md with sealed path concept

Add 'Why sealed paths matter' section (2-3 sentences): credentials bypass string interpolation, injected as env vars at OS level, cannot be extracted by prompt injection targeting command template. Gap 6.


**2026-02-15 22:52 UTC:** Added 'Why sealed paths matter' section to policy-auth.md explaining credential isolation from string interpolation and prompt injection defense.
