---
id: m-d50c
status: in_progress
deps: []
created: 2026-02-05T12:05:19Z
type: impl
priority: 2
assignee: Adam
tags: [phase-2, urgency-high]
updated: 2026-02-05T12:05:25Z
---
# Create multi-layer policy composition example

The scenario requires demonstrating policy composition across three layers: team policy, project policy, and local override.

**Required artifacts:**
1. Create test fixtures showing:
   - team-policy.mld: Broad deny list (no shell), allow safe git commands
   - project-policy.mld: Narrower allow list, additional label rules
   - local-override.mld: Local developer permissions
   - main.mld: Imports all three and demonstrates composition

**Expected behaviors to demonstrate:**
- Allow lists intersect (only commands allowed by ALL policies work)
- Deny lists union (command denied by ANY policy is blocked)
- Label flow rules compose (deny rules from any policy block flows)
- Limits take minimum values

**Location:** tests/cases/security/policy-composition-layers/

**Implementation guidance:**
Look at existing tests/cases/feat/policy/union/ for structure.

