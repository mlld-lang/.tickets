---
id: mlld-dp5
status: closed
deps: []
links: []
created: 2025-12-18T17:51:28.115945-08:00
type: feature
priority: 0
---
# Add 'when @value first [patterns]' syntax for pattern matching with bound value

User request from @partydev building proto-4.1 multi-agent routing.

Current syntax requires repeating the variable in each condition:
when first [
  @score >= 0.7 => "REQUIRED"
  @score >= 0.3 => "OPTIONAL"
]

Proposed syntax binds the value upfront:
when @score first [
  >= 0.7 => "REQUIRED"
  >= 0.3 => "OPTIONAL"
]

This is a natural extension of 'when first' that reduces repetition and improves readability.

See: /Users/adam/dev/party/mlld-feature-request-routing.md


