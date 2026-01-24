---
id: mlld-8l8
status: closed
deps: []
links: []
created: 2025-12-18T17:51:38.827652-08:00
type: feature
priority: 0
---
# Add ?? null coalescing operator

User request from @partydev for simplified optional value handling.

Current syntax for default values:
when first [
  @value => show "Result: @value"
  * => show "Result: (none)"
]

Proposed syntax:
show `Result: @value ?? "(none)"`

Common pattern across JS, C#, Swift, etc. Cleaner for simple default value cases.

See: /Users/adam/dev/party/mlld-feature-request-routing.md


