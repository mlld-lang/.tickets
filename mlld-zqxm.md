---
id: mlld-zqxm
status: closed
deps: []
links: []
created: 2026-01-10T16:32:44.54004-08:00
type: feature
priority: 1
---
# Haiku route reactions before waking agents

Currently any @mention (including reactions/emoji) can wake a daemon-managed agent. Reactions should be routed through haiku to assess if the reaction warrants waking. Wake if: agent was asking for approval/confirmation, reaction is semantically a directive. Don't wake if: reaction is just an acknowledgment emoji with no actionable intent.


