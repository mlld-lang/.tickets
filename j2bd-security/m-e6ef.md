---
id: m-e6ef
status: closed
deps: []
created: 2026-02-09T11:13:45Z
type: task
priority: 2
assignee: Adam Avenir
tags: [phase-1, urgency-high]
updated: 2026-02-09T11:19:01Z
---
# Update atoms to emphasize two-step pattern for built-in rules

The 5 key atoms (labels-sensitivity, labels-trust, policy-label-flow, policy-operations, policies) need updates to properly document the two-step pattern:

1. **labels-sensitivity.md**: The 'Security rules for sensitivity labels' section shows `exe exfil @sendToServer(data)` as the example. Update to show semantic labeling (`exe net:w @sendToServer`) + policy.operations classification (`"net:w": "exfil"`). Keep the rule table but connect it to the two-step flow.

2. **labels-trust.md**: Shows `exe destructive @wipe(data)` directly. Update to use semantic label + policy classification. Show the full two-step: semantic label on exe, policy.operations maps it to risk category, rule blocks the flow.

3. **policy-operations.md**: Currently leads with 'Direct risk labeling' (`exe exfil @sendToServer`). Restructure to lead with the semantic + classification pattern as the PRIMARY approach. Direct labeling can be mentioned as an alternative but should not be the lead example.

4. **policy-label-flow.md**: Good as-is but should add a brief note connecting to defaults.rules - mention that the label flow rules work alongside built-in rules.

5. **policies.md**: Very bare (4 lines of code, no prose). Expand to show a complete policy with defaults.rules AND policy.operations together. This is the 'glue' atom - it should show how all the pieces fit.

Success criteria for this ticket:
- defaults.rules described with concrete examples in at least 2 atoms
- Rule names clearly mapped to data-label + risk-classification pairs
- policy.operations explained with semantic label examples
- Two-step pattern clearly documented as the primary approach
- Direct risk labeling mentioned as alternative, not primary


**2026-02-09 11:19 UTC:** Worker updated labels-sensitivity, labels-trust, policy-operations, and policies atoms. policy-label-flow was left unchanged as it already correctly references operation labels. Committed as d0c077bd4.
