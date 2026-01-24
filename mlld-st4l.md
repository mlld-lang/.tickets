---
id: mlld-st4l
status: closed
deps: []
links: []
created: 2025-12-18T17:51:58.899693-08:00
type: feature
priority: 0
---
# Add 'when @value [...]' for bound-value all-match evaluation

User request from @partydev. Companion to mlld-dp5 (when @value first).

Current: when [] already executes all matching conditions.
Needed: when @value [] - bind a value upfront, then all matching conditions fire.

Example:
when @selectedAgents by @agent.meta.id [
  "mllddev" => { agent: @agent, docs: gatherDocs(...) }
  "party" => { agent: @agent, history: loadHistory(...) }
]

This is the all-match version of 'when @value first'. No 'parallel' keyword needed since bare 'when' already executes all matches.

See: /Users/adam/dev/party/mlld-feature-request-routing.md


