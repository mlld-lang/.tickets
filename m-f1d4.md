---
id: m-f1d4
status: open
deps: []
created: 2026-02-17T04:59:08Z
type: bug
priority: 0
assignee: Adam
---
# Audit suspicious single-character npm dependencies

package.json contains two suspicious single-character dependencies: "-": "^0.0.1" and "g": "^2.0.1". These look like debug artifacts or accidents. Investigate whether they are genuinely needed and remove if not.

